---
title: Provedores de política de autorização personalizada no ASP.NET Core
author: mjrousos
description: Saiba como usar um IAuthorizationPolicyProvider personalizado em um aplicativo ASP.NET Core para gerar dinamicamente a políticas de autorização.
ms.author: riande
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: ca57a9fd8e3c11f15fe14bbe4538bc748c4c84b6
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444149"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="a386f-103">Provedores de política de autorização personalizados usando IAuthorizationPolicyProvider no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a386f-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="a386f-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="a386f-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="a386f-105">Normalmente ao usar [baseado em políticas de autorização](xref:security/authorization/policies), as políticas são registradas por meio da chamada `AuthorizationOptions.AddPolicy` como parte da configuração do serviço de autorização.</span><span class="sxs-lookup"><span data-stu-id="a386f-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="a386f-106">Em alguns cenários, pode não ser possível (ou desejável) para registrar todas as políticas de autorização dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="a386f-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="a386f-107">Nesses casos, você pode usar um personalizado `IAuthorizationPolicyProvider` para controlar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="a386f-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="a386f-108">Exemplos de cenários onde um personalizado [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) podem ser úteis incluem:</span><span class="sxs-lookup"><span data-stu-id="a386f-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="a386f-109">Usando um serviço externo para fornecer a avaliação da política.</span><span class="sxs-lookup"><span data-stu-id="a386f-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="a386f-110">Usando uma grande variedade de diretivas (para números de sala de diferentes ou com as idades, por exemplo), portanto, não faz sentido adicionar cada política de autorização individuais com um `AuthorizationOptions.AddPolicy` chamar.</span><span class="sxs-lookup"><span data-stu-id="a386f-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="a386f-111">Criação de políticas em tempo de execução com base nas informações em uma fonte de dados externa (como um banco de dados) ou determinar os requisitos de autorização dinamicamente por meio de outro mecanismo.</span><span class="sxs-lookup"><span data-stu-id="a386f-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="a386f-112">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) do [repositório GitHub de AspNetCore](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="a386f-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="a386f-113">Baixe o arquivo ZIP do repositório aspnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="a386f-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="a386f-114">Descompacte o arquivo.</span><span class="sxs-lookup"><span data-stu-id="a386f-114">Unzip the file.</span></span> <span data-ttu-id="a386f-115">Navegue até a *src/Security/samples/CustomPolicyProvider* pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="a386f-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="a386f-116">Personalizar a recuperação da política</span><span class="sxs-lookup"><span data-stu-id="a386f-116">Customize policy retrieval</span></span>

<span data-ttu-id="a386f-117">Aplicativos ASP.NET Core usam uma implementação do `IAuthorizationPolicyProvider` interface para recuperar as políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="a386f-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="a386f-118">Por padrão, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registrado e usado.</span><span class="sxs-lookup"><span data-stu-id="a386f-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="a386f-119">`DefaultAuthorizationPolicyProvider` Retorna as políticas do `AuthorizationOptions` fornecidas em um `IServiceCollection.AddAuthorization` chamar.</span><span class="sxs-lookup"><span data-stu-id="a386f-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="a386f-120">Você pode personalizar esse comportamento, registrando um diferentes `IAuthorizationPolicyProvider` implementação do aplicativo [injeção de dependência](xref:fundamentals/dependency-injection) contêiner.</span><span class="sxs-lookup"><span data-stu-id="a386f-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="a386f-121">O `IAuthorizationPolicyProvider` interface contém duas APIs:</span><span class="sxs-lookup"><span data-stu-id="a386f-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="a386f-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retorna uma política de autorização para um determinado nome.</span><span class="sxs-lookup"><span data-stu-id="a386f-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="a386f-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retorna a política de autorização padrão (a política usada para `[Authorize]` atributos sem uma política especificada).</span><span class="sxs-lookup"><span data-stu-id="a386f-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="a386f-124">Implementando essas duas APIs, você pode personalizar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="a386f-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="a386f-125">Parametrizadas autorizar o exemplo de atributo</span><span class="sxs-lookup"><span data-stu-id="a386f-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="a386f-126">Um cenário em que `IAuthorizationPolicyProvider` é útil é a habilitação do personalizado `[Authorize]` atributos cujos requisitos dependem de um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a386f-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="a386f-127">Por exemplo, na [autorização baseada em política](xref:security/authorization/policies) documentação, uma com base em idade ("AtLeast21") política foi usada como um exemplo.</span><span class="sxs-lookup"><span data-stu-id="a386f-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="a386f-128">Se as ações de controlador diferente em um aplicativo devem ser disponibilizadas para os usuários do *diferentes* há séculos, pode ser útil ter muitas políticas diferentes com base em idade.</span><span class="sxs-lookup"><span data-stu-id="a386f-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="a386f-129">Em vez de registrar todas as diferentes com base em idade políticas que o aplicativo será necessário em `AuthorizationOptions`, você pode gerar as políticas dinamicamente com um personalizado `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="a386f-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="a386f-130">Para tornar o uso de políticas mais fácil, você pode anotar as ações com o atributo de autorização personalizado, como `[MinimumAgeAuthorize(20)]`.</span><span class="sxs-lookup"><span data-stu-id="a386f-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="a386f-131">Atributos de autorização personalizada</span><span class="sxs-lookup"><span data-stu-id="a386f-131">Custom Authorization Attributes</span></span>

<span data-ttu-id="a386f-132">Políticas de autorização são identificadas por seus nomes.</span><span class="sxs-lookup"><span data-stu-id="a386f-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="a386f-133">Personalizado `MinimumAgeAuthorizeAttribute` descrito anteriormente, precisa mapear argumentos em uma cadeia de caracteres que pode ser usada para recuperar a política de autorização correspondente.</span><span class="sxs-lookup"><span data-stu-id="a386f-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="a386f-134">Você pode fazer isso, derivando de `AuthorizeAttribute` e fazer a `Age` quebra automática de propriedade a `AuthorizeAttribute.Policy` propriedade.</span><span class="sxs-lookup"><span data-stu-id="a386f-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="a386f-135">Esse tipo de atributo tem um `Policy` cadeia de caracteres com base no prefixo embutido em código (`"MinimumAge"`) e um número inteiro passado por meio do construtor.</span><span class="sxs-lookup"><span data-stu-id="a386f-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="a386f-136">Você pode aplicá-lo às ações da mesma maneira que outras `Authorize` atributos, exceto que assume um inteiro como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a386f-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="a386f-137">IAuthorizationPolicyProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="a386f-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="a386f-138">Personalizado `MinimumAgeAuthorizeAttribute` facilita a políticas de autorização da solicitação para qualquer idade mínima desejada.</span><span class="sxs-lookup"><span data-stu-id="a386f-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="a386f-139">O próximo problema a resolver é verificar se as políticas de autorização estão disponíveis para todas as idades diferentes.</span><span class="sxs-lookup"><span data-stu-id="a386f-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="a386f-140">É aí que um `IAuthorizationPolicyProvider` é útil.</span><span class="sxs-lookup"><span data-stu-id="a386f-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="a386f-141">Ao usar `MinimumAgeAuthorizationAttribute`, os nomes de política de autorização seguirá o padrão `"MinimumAge" + Age`, então personalizado `IAuthorizationPolicyProvider` deve gerar diretivas de autorização por:</span><span class="sxs-lookup"><span data-stu-id="a386f-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="a386f-142">A idade do nome da política de análise.</span><span class="sxs-lookup"><span data-stu-id="a386f-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="a386f-143">Usando `AuthorizationPolicyBuilder` para criar um novo `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="a386f-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="a386f-144">Adicionar requisitos para a política com base na idade com `AuthorizationPolicyBuilder.AddRequirements`.</span><span class="sxs-lookup"><span data-stu-id="a386f-144">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="a386f-145">Em outros cenários, você pode usar `RequireClaim`, `RequireRole`, ou `RequireUserName` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="a386f-145">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder();
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="a386f-146">Vários provedores de política de autorização</span><span class="sxs-lookup"><span data-stu-id="a386f-146">Multiple authorization policy providers</span></span>

<span data-ttu-id="a386f-147">Quando usar custom `IAuthorizationPolicyProvider` implementações, tenha em mente que o ASP.NET Core usa apenas uma instância de `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="a386f-147">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="a386f-148">Se um provedor personalizado não é capaz de fornecer políticas de autorização para todos os nomes de política, ele deve fazer fallback para um provedor de backup.</span><span class="sxs-lookup"><span data-stu-id="a386f-148">If a custom provider isn't able to provide authorization policies for all policy names, it should fall back to a backup provider.</span></span> <span data-ttu-id="a386f-149">Nomes de política podem incluir aqueles que vêm de uma política padrão para `[Authorize]` atributos sem um nome.</span><span class="sxs-lookup"><span data-stu-id="a386f-149">Policy names might include those that come from a default policy for `[Authorize]` attributes without a name.</span></span>

<span data-ttu-id="a386f-150">Por exemplo, considere que um aplicativo precisar de políticas personalizadas de idade e recuperação de política baseada em função mais tradicional.</span><span class="sxs-lookup"><span data-stu-id="a386f-150">For example, consider an application needed both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="a386f-151">Esse aplicativo poderia usar um provedor de diretivas de autorização personalizada que:</span><span class="sxs-lookup"><span data-stu-id="a386f-151">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="a386f-152">Tenta analisar nomes de política.</span><span class="sxs-lookup"><span data-stu-id="a386f-152">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="a386f-153">Chamadas para um provedor de política diferente (como `DefaultAuthorizationPolicyProvider`) se o nome da política não contiver uma idade.</span><span class="sxs-lookup"><span data-stu-id="a386f-153">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

## <a name="default-policy"></a><span data-ttu-id="a386f-154">Política padrão</span><span class="sxs-lookup"><span data-stu-id="a386f-154">Default policy</span></span>

<span data-ttu-id="a386f-155">Além de fornecer políticas de autorização nomeado, um personalizado `IAuthorizationPolicyProvider` precisa implementar `GetDefaultPolicyAsync` para fornecer uma política de autorização para `[Authorize]` atributos sem um nome de política especificado.</span><span class="sxs-lookup"><span data-stu-id="a386f-155">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="a386f-156">Em muitos casos, esse atributo de autorização requer apenas um usuário autenticado, portanto, você pode fazer a diretiva necessária com uma chamada para `RequireAuthenticatedUser`:</span><span class="sxs-lookup"><span data-stu-id="a386f-156">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

<span data-ttu-id="a386f-157">Assim como acontece com todos os aspectos de um personalizado `IAuthorizationPolicyProvider`, você pode personalizá-lo, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="a386f-157">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="a386f-158">Em alguns casos:</span><span class="sxs-lookup"><span data-stu-id="a386f-158">In some cases:</span></span>

* <span data-ttu-id="a386f-159">Políticas de autorização padrão não podem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="a386f-159">Default authorization policies might not be used.</span></span>
* <span data-ttu-id="a386f-160">Recuperando a política padrão pode ser designado como um fallback `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="a386f-160">Retrieving the default policy can be delegated to a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="a386f-161">Use um IAuthorizationPolicyProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="a386f-161">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="a386f-162">Para usar políticas personalizadas de um `IAuthorizationPolicyProvider`, você deve:</span><span class="sxs-lookup"><span data-stu-id="a386f-162">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="a386f-163">Registrar apropriado `AuthorizationHandler` tipos de injeção de dependência (descrito na [autorização baseada em política](xref:security/authorization/policies#authorization-handlers)), assim como acontece com todos os cenários de autorização baseada em política.</span><span class="sxs-lookup"><span data-stu-id="a386f-163">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="a386f-164">Registrar personalizado `IAuthorizationPolicyProvider` tipo na coleção de serviço de injeção de dependência do aplicativo (em `Startup.ConfigureServices`) para substituir o provedor de política padrão.</span><span class="sxs-lookup"><span data-stu-id="a386f-164">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="a386f-165">Um personalizado completo `IAuthorizationPolicyProvider` exemplo está disponível na [repositório do GitHub aspnet/AuthSamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="a386f-165">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
