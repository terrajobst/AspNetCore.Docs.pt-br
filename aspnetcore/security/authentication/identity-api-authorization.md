---
title: Introdução à autenticação para aplicativos de página única no ASP.NET Core
author: javiercn
description: Use a identidade com um aplicativo de página única hospedado dentro de um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 98df1aa1671c22384252676c56e8cb4a3a0a35eb
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190496"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="47278-103">Autenticação e autorização para SPAs</span><span class="sxs-lookup"><span data-stu-id="47278-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="47278-104">O ASP.NET Core 3,0 ou posterior oferece autenticação em aplicativos de página única (SPAs) usando o suporte para autorização de API.</span><span class="sxs-lookup"><span data-stu-id="47278-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="47278-105">ASP.NET Core identidade para autenticação e armazenamento de usuários é combinada com o [IdentityServer](https://identityserver.io/) para implementar o Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="47278-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="47278-106">Um parâmetro de autenticação foi adicionado aos modelos de projeto **angular** e **reajam** que é semelhante ao parâmetro de autenticação no **aplicativo Web (Model-View-Controller)** (MVC) e **aplicativo Web** (Razor Pages) modelos de projeto.</span><span class="sxs-lookup"><span data-stu-id="47278-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="47278-107">Os valores de parâmetro permitidos são **None** e **individual**.</span><span class="sxs-lookup"><span data-stu-id="47278-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="47278-108">O modelo de projeto **reajam. js e Redux** não dá suporte ao parâmetro de autenticação no momento.</span><span class="sxs-lookup"><span data-stu-id="47278-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="47278-109">Criar um aplicativo com suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="47278-109">Create an app with API authorization support</span></span>

<span data-ttu-id="47278-110">A autenticação e a autorização do usuário podem ser usadas com SPAs angular e reagir.</span><span class="sxs-lookup"><span data-stu-id="47278-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="47278-111">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="47278-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="47278-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="47278-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="47278-113">**Reagir**:</span><span class="sxs-lookup"><span data-stu-id="47278-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="47278-114">O comando anterior cria um aplicativo ASP.NET Core com um diretório *ClientApp* que contém o Spa.</span><span class="sxs-lookup"><span data-stu-id="47278-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="47278-115">Descrição geral dos componentes ASP.NET Core do aplicativo</span><span class="sxs-lookup"><span data-stu-id="47278-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="47278-116">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído:</span><span class="sxs-lookup"><span data-stu-id="47278-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="47278-117">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="47278-117">Startup class</span></span>

<span data-ttu-id="47278-118">A classe `Startup` tem as seguintes adições:</span><span class="sxs-lookup"><span data-stu-id="47278-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="47278-119">Dentro do método de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="47278-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="47278-120">Identidade com a interface do usuário padrão:</span><span class="sxs-lookup"><span data-stu-id="47278-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="47278-121">IdentityServer com um método auxiliar adicional `AddApiAuthorization` que configura algumas convenções de ASP.NET Core padrão na parte superior do IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="47278-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="47278-122">Autenticação com um método auxiliar `AddIdentityServerJwt` adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="47278-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="47278-123">Dentro do método de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="47278-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="47278-124">O middleware de autenticação que é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="47278-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="47278-125">O middleware IdentityServer que expõe os pontos de extremidade do Open ID Connect:</span><span class="sxs-lookup"><span data-stu-id="47278-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="47278-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="47278-126">AddApiAuthorization</span></span>

<span data-ttu-id="47278-127">Esse método auxiliar configura o IdentityServer para usar nossa configuração com suporte.</span><span class="sxs-lookup"><span data-stu-id="47278-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="47278-128">O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="47278-129">Ao mesmo tempo, isso expõe uma complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="47278-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="47278-130">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para você que são considerados um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="47278-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="47278-131">Depois que a autenticação precisar ser alterada, toda a potência do IdentityServer ainda estará disponível para personalizar a autenticação de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="47278-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="47278-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="47278-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="47278-133">Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="47278-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="47278-134">A política está configurada para permitir que a identidade manipule todas as solicitações roteadas para qualquer subcaminho no espaço de URL de identidade "/Identity".</span><span class="sxs-lookup"><span data-stu-id="47278-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="47278-135">O `JwtBearerHandler` lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="47278-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="47278-136">Além disso, esse método registra um `<<ApplicationName>>API` recurso de API com IdentityServer com um escopo padrão de `<<ApplicationName>>API` e configura o middleware de token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="47278-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="47278-137">WeatherForecastController</span></span>

<span data-ttu-id="47278-138">No arquivo *Controllers\WeatherForecastController.cs* , observe o atributo `[Authorize]` aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="47278-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="47278-139">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por `AddIdentityServerJwt` para o esquema de política mencionado acima, tornando o `JwtBearerHandler` configurado por tal método auxiliar o manipulador padrão para solicitações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="47278-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="47278-140">ApplicationDbContext</span></span>

<span data-ttu-id="47278-141">No arquivo *Data\ApplicationDbContext.cs* , observe que o mesmo `DbContext` é usado em identidade com a exceção que ele estende `ApiAuthorizationDbContext` (uma classe mais derivada de `IdentityDbContext`) para incluir o esquema para IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="47278-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="47278-142">Para obter controle total do esquema de banco de dados, herde de uma das classes de identidade `DbContext` disponíveis e configure o contexto para incluir o esquema de identidade chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no método `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="47278-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="47278-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="47278-143">OidcConfigurationController</span></span>

<span data-ttu-id="47278-144">No arquivo *Controllers\OidcConfigurationController.cs* , observe o ponto de extremidade provisionado para atender aos parâmetros OIDC que o cliente precisa usar.</span><span class="sxs-lookup"><span data-stu-id="47278-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="47278-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="47278-145">appsettings.json</span></span>

<span data-ttu-id="47278-146">No arquivo *appSettings. JSON* da raiz do projeto, há uma nova seção `IdentityServer` que descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="47278-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="47278-147">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="47278-147">In the following example, there's a single client.</span></span> <span data-ttu-id="47278-148">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o parâmetro de `ClientId` do OAuth.</span><span class="sxs-lookup"><span data-stu-id="47278-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="47278-149">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="47278-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="47278-150">Ele é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="47278-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="47278-151">Há vários perfis disponíveis, conforme explicado na seção [perfis de aplicativo](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="47278-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="47278-152">AppSettings. Development. JSON</span><span class="sxs-lookup"><span data-stu-id="47278-152">appsettings.Development.json</span></span>

<span data-ttu-id="47278-153">Em *appSettings. Arquivo Development. JSON* da raiz do projeto, há uma seção `IdentityServer` que descreve a chave usada para assinar tokens.</span><span class="sxs-lookup"><span data-stu-id="47278-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="47278-154">Ao implantar na produção, uma chave precisa ser provisionada e implantada juntamente com o aplicativo, conforme explicado na seção [implantar na produção](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="47278-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="47278-155">Descrição geral do aplicativo angular</span><span class="sxs-lookup"><span data-stu-id="47278-155">General description of the Angular app</span></span>

<span data-ttu-id="47278-156">O suporte de autorização de API e autenticação no modelo angular reside em seu próprio módulo angular no diretório *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="47278-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="47278-157">O módulo é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="47278-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="47278-158">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="47278-158">3 components:</span></span>
  * <span data-ttu-id="47278-159">*login. Component. TS*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="47278-160">*logout. Component. TS*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="47278-161">*login-menu. Component. TS*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="47278-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="47278-162">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="47278-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="47278-163">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="47278-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="47278-164">Um `AuthorizeGuard` de proteção de rota que pode ser adicionado a rotas e requer que um usuário seja autenticado antes de visitar a rota.</span><span class="sxs-lookup"><span data-stu-id="47278-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="47278-165">Um `AuthorizeInterceptor` Interceptor HTTP que anexa o token de acesso a solicitações HTTP de saída direcionando a API quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="47278-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="47278-166">Um `AuthorizeService` de serviço que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="47278-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="47278-167">Um módulo angular que define as rotas associadas às partes de autenticação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="47278-168">Ele expõe o componente de menu de logon, o Interceptor, a proteção e o serviço para consumo do restante do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="47278-169">Descrição geral do aplicativo reajam</span><span class="sxs-lookup"><span data-stu-id="47278-169">General description of the React app</span></span>

<span data-ttu-id="47278-170">O suporte para autenticação e autorização de API no modelo reagir reside no diretório *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="47278-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="47278-171">Ele é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="47278-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="47278-172">4 componentes:</span><span class="sxs-lookup"><span data-stu-id="47278-172">4 components:</span></span>
  * <span data-ttu-id="47278-173">*Login. js*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="47278-174">*Logout. js*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="47278-175">*LoginMenu. js*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="47278-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="47278-176">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="47278-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="47278-177">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="47278-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="47278-178">*AuthorizeRoute. js*: um componente de rota que exige que um usuário seja autenticado antes de renderizar o componente indicado no parâmetro `Component`.</span><span class="sxs-lookup"><span data-stu-id="47278-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="47278-179">Uma instância de `authService` exportada da classe `AuthorizeService` que lida com os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="47278-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="47278-180">Agora que você já viu os principais componentes da solução, pode fazer uma análise mais profunda de cenários individuais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="47278-181">Exigir autorização em uma nova API</span><span class="sxs-lookup"><span data-stu-id="47278-181">Require authorization on a new API</span></span>

<span data-ttu-id="47278-182">Por padrão, o sistema está configurado para exigir facilmente a autorização para novas APIs.</span><span class="sxs-lookup"><span data-stu-id="47278-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="47278-183">Para fazer isso, crie um novo controlador e adicione o atributo `[Authorize]` à classe controlador ou a qualquer ação dentro do controlador.</span><span class="sxs-lookup"><span data-stu-id="47278-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="47278-184">Personalizar o manipulador de autenticação de API</span><span class="sxs-lookup"><span data-stu-id="47278-184">Customize the API authentication handler</span></span>

<span data-ttu-id="47278-185">Para personalizar a configuração do manipulador JWT da API, configure sua instância de <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>:</span><span class="sxs-lookup"><span data-stu-id="47278-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.ConfigureOptions<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="47278-186">Proteger uma rota do lado do cliente (angular)</span><span class="sxs-lookup"><span data-stu-id="47278-186">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="47278-187">A proteção de uma rota do lado do cliente é feita pela adição da proteção de autorização à lista de proteções a serem executadas durante a configuração de uma rota.</span><span class="sxs-lookup"><span data-stu-id="47278-187">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="47278-188">Como exemplo, você pode ver como a rota de `fetch-data` é configurada no módulo angular do aplicativo principal:</span><span class="sxs-lookup"><span data-stu-id="47278-188">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="47278-189">É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um atributo `[Authorize]` aplicado a ele), mas que só impede que o usuário navegue até a rota do lado do cliente quando não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="47278-189">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="47278-190">Autenticar solicitações de API (angular)</span><span class="sxs-lookup"><span data-stu-id="47278-190">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="47278-191">A autenticação de solicitações para APIs hospedadas juntamente com o aplicativo é feita automaticamente pelo uso do interceptador de cliente HTTP definido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-191">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="47278-192">Proteger uma rota do lado do cliente (reagir)</span><span class="sxs-lookup"><span data-stu-id="47278-192">Protect a client-side route (React)</span></span>

<span data-ttu-id="47278-193">Proteja uma rota do lado do cliente usando o componente `AuthorizeRoute` em vez do componente `Route` simples.</span><span class="sxs-lookup"><span data-stu-id="47278-193">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="47278-194">Por exemplo, observe como a rota de `fetch-data` é configurada no componente `App`:</span><span class="sxs-lookup"><span data-stu-id="47278-194">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="47278-195">Protegendo uma rota:</span><span class="sxs-lookup"><span data-stu-id="47278-195">Protecting a route:</span></span>

* <span data-ttu-id="47278-196">Não protege o ponto de extremidade real (que ainda requer um atributo `[Authorize]` aplicado a ele).</span><span class="sxs-lookup"><span data-stu-id="47278-196">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="47278-197">Impede que o usuário navegue para a rota determinada do lado do cliente quando ele não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="47278-197">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="47278-198">Autenticar solicitações de API (reagir)</span><span class="sxs-lookup"><span data-stu-id="47278-198">Authenticate API requests (React)</span></span>

<span data-ttu-id="47278-199">A autenticação de solicitações com reagir é feita pela primeira importação da instância de `authService` do `AuthorizeService`.</span><span class="sxs-lookup"><span data-stu-id="47278-199">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="47278-200">O token de acesso é recuperado do `authService` e é anexado à solicitação, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="47278-200">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="47278-201">Em reajam de componentes, esse trabalho normalmente é feito no método de ciclo de vida `componentDidMount` ou como resultado de alguma interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="47278-201">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="47278-202">Importar o authService para o componente</span><span class="sxs-lookup"><span data-stu-id="47278-202">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="47278-203">Recuperar e anexar o token de acesso à resposta</span><span class="sxs-lookup"><span data-stu-id="47278-203">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="47278-204">Implantar na produção</span><span class="sxs-lookup"><span data-stu-id="47278-204">Deploy to production</span></span>

<span data-ttu-id="47278-205">Para implantar o aplicativo na produção, os seguintes recursos precisam ser provisionados:</span><span class="sxs-lookup"><span data-stu-id="47278-205">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="47278-206">Um banco de dados para armazenar as contas de usuário de identidade e as concessões IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="47278-206">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="47278-207">Um certificado de produção a ser usado para tokens de assinatura.</span><span class="sxs-lookup"><span data-stu-id="47278-207">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="47278-208">Não há requisitos específicos para esse certificado; pode ser um certificado autoassinado ou um certificado provisionado por meio de uma autoridade de certificação.</span><span class="sxs-lookup"><span data-stu-id="47278-208">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="47278-209">Ele pode ser gerado por meio de ferramentas padrão, como o PowerShell ou o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="47278-209">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="47278-210">Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um arquivo *. pfx* com uma senha forte.</span><span class="sxs-lookup"><span data-stu-id="47278-210">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="47278-211">Exemplo: implantar nos sites do Azure</span><span class="sxs-lookup"><span data-stu-id="47278-211">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="47278-212">Esta seção descreve a implantação do aplicativo nos sites do Azure usando um certificado armazenado no repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="47278-212">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="47278-213">Para modificar o aplicativo para carregar um certificado do repositório de certificados, o plano do serviço de aplicativo precisa estar em pelo menos a camada Standard quando você configura o em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="47278-213">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="47278-214">No arquivo *appSettings. JSON* do aplicativo, modifique a seção `IdentityServer` para incluir os detalhes da chave:</span><span class="sxs-lookup"><span data-stu-id="47278-214">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="47278-215">A propriedade Name no certificado corresponde ao assunto distinto do certificado.</span><span class="sxs-lookup"><span data-stu-id="47278-215">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="47278-216">O local do repositório representa onde carregar o certificado (`CurrentUser` ou `LocalMachine`).</span><span class="sxs-lookup"><span data-stu-id="47278-216">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="47278-217">O nome do repositório representa o nome do repositório de certificados onde o certificado está armazenado.</span><span class="sxs-lookup"><span data-stu-id="47278-217">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="47278-218">Nesse caso, ele aponta para o repositório de usuários pessoais.</span><span class="sxs-lookup"><span data-stu-id="47278-218">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="47278-219">Para implantar os sites do Azure, implante o aplicativo seguindo as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para criar os recursos necessários do Azure e implantar o aplicativo para produção.</span><span class="sxs-lookup"><span data-stu-id="47278-219">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="47278-220">Depois de seguir as instruções anteriores, o aplicativo é implantado no Azure, mas ainda não está funcional.</span><span class="sxs-lookup"><span data-stu-id="47278-220">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="47278-221">O certificado usado pelo aplicativo ainda precisa ser configurado.</span><span class="sxs-lookup"><span data-stu-id="47278-221">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="47278-222">Localize a impressão digital do certificado a ser usado e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="47278-222">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="47278-223">Embora essas etapas mencionem o SSL, há uma seção de **certificados privados** no portal em que você pode carregar o certificado provisionado para usar com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-223">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="47278-224">Após essa etapa, reinicie o aplicativo e ele deverá ser funcional.</span><span class="sxs-lookup"><span data-stu-id="47278-224">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="47278-225">Outras opções de configuração</span><span class="sxs-lookup"><span data-stu-id="47278-225">Other configuration options</span></span>

<span data-ttu-id="47278-226">O suporte para a autorização da API se baseia no IdentityServer com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência do SPAs.</span><span class="sxs-lookup"><span data-stu-id="47278-226">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="47278-227">Não é necessário dizer que todo o poder do IdentityServer está disponível nos bastidores se as integrações ASP.NET Core não abrangem seu cenário.</span><span class="sxs-lookup"><span data-stu-id="47278-227">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="47278-228">O suporte a ASP.NET Core concentra-se em aplicativos de "primeira parte", onde todos os aplicativos são criados e implantados pela nossa organização.</span><span class="sxs-lookup"><span data-stu-id="47278-228">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="47278-229">Dessa forma, o suporte não é oferecido para coisas como autorização ou Federação.</span><span class="sxs-lookup"><span data-stu-id="47278-229">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="47278-230">Para esses cenários, use o IdentityServer e siga sua documentação.</span><span class="sxs-lookup"><span data-stu-id="47278-230">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="47278-231">Perfis de aplicativo</span><span class="sxs-lookup"><span data-stu-id="47278-231">Application profiles</span></span>

<span data-ttu-id="47278-232">Perfis de aplicativo são configurações predefinidas para aplicativos que definem ainda mais seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="47278-232">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="47278-233">Neste momento, há suporte para os seguintes perfis:</span><span class="sxs-lookup"><span data-stu-id="47278-233">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="47278-234">`IdentityServerSPA`: representa um SPA hospedado juntamente com IdentityServer como uma única unidade.</span><span class="sxs-lookup"><span data-stu-id="47278-234">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="47278-235">O `redirect_uri` usa como padrão `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="47278-235">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="47278-236">O `post_logout_redirect_uri` usa como padrão `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="47278-236">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="47278-237">O conjunto de escopos inclui o `openid`, `profile`e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-237">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="47278-238">O conjunto de tipos de resposta OIDC permitidos é `id_token token` ou cada um deles individualmente (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="47278-238">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="47278-239">O modo de resposta permitido é `fragment`.</span><span class="sxs-lookup"><span data-stu-id="47278-239">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="47278-240">`SPA`: representa um SPA que não é hospedado com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="47278-240">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="47278-241">O conjunto de escopos inclui o `openid`, `profile`e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-241">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="47278-242">O conjunto de tipos de resposta OIDC permitidos é `id_token token` ou cada um deles individualmente (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="47278-242">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="47278-243">O modo de resposta permitido é `fragment`.</span><span class="sxs-lookup"><span data-stu-id="47278-243">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="47278-244">`IdentityServerJwt`: representa uma API que é hospedada juntamente com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="47278-244">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="47278-245">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-245">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="47278-246">`API`: representa uma API que não é hospedada com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="47278-246">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="47278-247">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47278-247">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="47278-248">Configuração através de AppSettings</span><span class="sxs-lookup"><span data-stu-id="47278-248">Configuration through AppSettings</span></span>

<span data-ttu-id="47278-249">Configure os aplicativos por meio do sistema de configuração adicionando-os à lista de `Clients` ou `Resources`.</span><span class="sxs-lookup"><span data-stu-id="47278-249">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="47278-250">Configure a propriedade de `redirect_uri` e `post_logout_redirect_uri` de cada cliente, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="47278-250">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="47278-251">Ao configurar recursos, você pode configurar os escopos para o recurso, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="47278-251">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="47278-252">Configuração por meio de código</span><span class="sxs-lookup"><span data-stu-id="47278-252">Configuration through code</span></span>

<span data-ttu-id="47278-253">Você também pode configurar os clientes e os recursos por meio de código usando uma sobrecarga de `AddApiAuthorization` que executa uma ação para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="47278-253">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="47278-254">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="47278-254">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
