---
title: Provedores de política de autorização personalizada no ASP.NET Core
author: mjrousos
description: Saiba como usar um IAuthorizationPolicyProvider personalizado em um aplicativo ASP.NET Core para gerar dinamicamente políticas de autorização.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 9f0a0cd5337f7f8d2fc8a4b6902a63b98f6bd702
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828978"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="0f575-103">Provedores de política de autorização personalizados usando IAuthorizationPolicyProvider no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f575-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="0f575-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="0f575-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="0f575-105">Normalmente, ao usar [a autorização baseada em políticas](xref:security/authorization/policies), as políticas são registradas chamando `AuthorizationOptions.AddPolicy` como parte da configuração do serviço de autorização.</span><span class="sxs-lookup"><span data-stu-id="0f575-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="0f575-106">Em alguns cenários, talvez não seja possível (ou desejável) registrar todas as políticas de autorização dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="0f575-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="0f575-107">Nesses casos, você pode usar um `IAuthorizationPolicyProvider` personalizado para controlar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="0f575-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="0f575-108">Exemplos de cenários em que um [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado pode ser útil incluem:</span><span class="sxs-lookup"><span data-stu-id="0f575-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="0f575-109">Usando um serviço externo para fornecer avaliação de política.</span><span class="sxs-lookup"><span data-stu-id="0f575-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="0f575-110">Usando uma grande variedade de políticas (para diferentes números de sala ou idades, por exemplo), não faz sentido adicionar cada política de autorização individual com uma chamada `AuthorizationOptions.AddPolicy`.</span><span class="sxs-lookup"><span data-stu-id="0f575-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="0f575-111">Criando políticas em tempo de execução com base nas informações de uma fonte de dados externa (como um banco de dado) ou determinando os requisitos de autorização dinamicamente por meio de outro mecanismo.</span><span class="sxs-lookup"><span data-stu-id="0f575-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="0f575-112">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) do [repositório GitHub do AspNetCore](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="0f575-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="0f575-113">Baixe o arquivo ZIP do repositório dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="0f575-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="0f575-114">Descompacte o arquivo.</span><span class="sxs-lookup"><span data-stu-id="0f575-114">Unzip the file.</span></span> <span data-ttu-id="0f575-115">Navegue até a pasta de projeto *src/Security/Samples/CustomPolicyProvider* .</span><span class="sxs-lookup"><span data-stu-id="0f575-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="0f575-116">Personalizar a recuperação de política</span><span class="sxs-lookup"><span data-stu-id="0f575-116">Customize policy retrieval</span></span>

<span data-ttu-id="0f575-117">ASP.NET Core aplicativos usam uma implementação da interface `IAuthorizationPolicyProvider` para recuperar políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="0f575-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="0f575-118">Por padrão, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) é registrado e usado.</span><span class="sxs-lookup"><span data-stu-id="0f575-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="0f575-119">`DefaultAuthorizationPolicyProvider` retorna as políticas do `AuthorizationOptions` fornecido em uma chamada de `IServiceCollection.AddAuthorization`.</span><span class="sxs-lookup"><span data-stu-id="0f575-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="0f575-120">Personalize esse comportamento registrando uma implementação de `IAuthorizationPolicyProvider` diferente no contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f575-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="0f575-121">A interface `IAuthorizationPolicyProvider` contém três APIs:</span><span class="sxs-lookup"><span data-stu-id="0f575-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="0f575-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retorna uma política de autorização para um determinado nome.</span><span class="sxs-lookup"><span data-stu-id="0f575-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="0f575-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retorna a política de autorização padrão (a política usada para atributos de `[Authorize]` sem uma política especificada).</span><span class="sxs-lookup"><span data-stu-id="0f575-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="0f575-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retorna a política de autorização de fallback (a política usada pelo middleware de autorização quando nenhuma política é especificada).</span><span class="sxs-lookup"><span data-stu-id="0f575-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="0f575-125">Ao implementar essas APIs, você pode personalizar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="0f575-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="0f575-126">Exemplo de atributo de autorização com parâmetros</span><span class="sxs-lookup"><span data-stu-id="0f575-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="0f575-127">Um cenário em que `IAuthorizationPolicyProvider` é útil é habilitar atributos de `[Authorize]` personalizados cujos requisitos dependem de um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0f575-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="0f575-128">Por exemplo, na documentação de [autorização baseada em políticas](xref:security/authorization/policies) , uma política baseada em idade ("AtLeast21") foi usada como exemplo.</span><span class="sxs-lookup"><span data-stu-id="0f575-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="0f575-129">Se ações de controlador diferentes em um aplicativo devem ser disponibilizadas para usuários de *diferentes* idades, pode ser útil ter muitas políticas diferentes baseadas em idade.</span><span class="sxs-lookup"><span data-stu-id="0f575-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="0f575-130">Em vez de registrar todas as diferentes políticas baseadas em idade que o aplicativo precisará em `AuthorizationOptions`, você pode gerar as políticas dinamicamente com um `IAuthorizationPolicyProvider`personalizado.</span><span class="sxs-lookup"><span data-stu-id="0f575-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="0f575-131">Para facilitar o uso das políticas, você pode anotar ações com atributo de autorização personalizado como `[MinimumAgeAuthorize(20)]`.</span><span class="sxs-lookup"><span data-stu-id="0f575-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="0f575-132">Atributos de autorização personalizados</span><span class="sxs-lookup"><span data-stu-id="0f575-132">Custom Authorization attributes</span></span>

<span data-ttu-id="0f575-133">As políticas de autorização são identificadas por seus nomes.</span><span class="sxs-lookup"><span data-stu-id="0f575-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="0f575-134">O `MinimumAgeAuthorizeAttribute` personalizado descrito anteriormente precisa mapear argumentos em uma cadeia de caracteres que pode ser usada para recuperar a política de autorização correspondente.</span><span class="sxs-lookup"><span data-stu-id="0f575-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="0f575-135">Você pode fazer isso derivando de `AuthorizeAttribute` e fazendo com que a propriedade `Age` Empacote a propriedade `AuthorizeAttribute.Policy`.</span><span class="sxs-lookup"><span data-stu-id="0f575-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="0f575-136">Esse tipo de atributo tem uma cadeia de caracteres `Policy` com base no prefixo embutido em código (`"MinimumAge"`) e um inteiro passado por meio do construtor.</span><span class="sxs-lookup"><span data-stu-id="0f575-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="0f575-137">Você pode aplicá-lo às ações da mesma maneira que outros atributos de `Authorize`, exceto pelo fato de que ele usa um inteiro como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0f575-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="0f575-138">IAuthorizationPolicyProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="0f575-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="0f575-139">O `MinimumAgeAuthorizeAttribute` personalizado facilita a solicitação de políticas de autorização para qualquer idade mínima desejada.</span><span class="sxs-lookup"><span data-stu-id="0f575-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="0f575-140">O próximo problema a ser resolvido é garantir que as políticas de autorização estejam disponíveis para todas essas idades diferentes.</span><span class="sxs-lookup"><span data-stu-id="0f575-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="0f575-141">É aí que um `IAuthorizationPolicyProvider` é útil.</span><span class="sxs-lookup"><span data-stu-id="0f575-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="0f575-142">Ao usar `MinimumAgeAuthorizationAttribute`, os nomes da diretiva de autorização seguirão o padrão `"MinimumAge" + Age`, portanto, o `IAuthorizationPolicyProvider` personalizado deverá gerar políticas de autorização:</span><span class="sxs-lookup"><span data-stu-id="0f575-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="0f575-143">Analisando a idade do nome da política.</span><span class="sxs-lookup"><span data-stu-id="0f575-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="0f575-144">Usando `AuthorizationPolicyBuilder` para criar um novo `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="0f575-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="0f575-145">Neste e nos exemplos a seguir, supõe-se que o usuário é autenticado por meio de um cookie.</span><span class="sxs-lookup"><span data-stu-id="0f575-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="0f575-146">O `AuthorizationPolicyBuilder` deve ser construído com pelo menos um nome de esquema de autorização ou sempre com sucesso.</span><span class="sxs-lookup"><span data-stu-id="0f575-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="0f575-147">Caso contrário, não há informações sobre como fornecer um desafio ao usuário e uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="0f575-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="0f575-148">Adição de requisitos à política com base na idade com `AuthorizationPolicyBuilder.AddRequirements`.</span><span class="sxs-lookup"><span data-stu-id="0f575-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="0f575-149">Em outros cenários, você pode usar `RequireClaim`, `RequireRole`ou `RequireUserName` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="0f575-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="0f575-150">Vários provedores de política de autorização</span><span class="sxs-lookup"><span data-stu-id="0f575-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="0f575-151">Ao usar implementações de `IAuthorizationPolicyProvider` personalizadas, tenha em mente que ASP.NET Core usa apenas uma instância do `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="0f575-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="0f575-152">Se um provedor personalizado não puder fornecer políticas de autorização para todos os nomes de política que serão usados, ele deve adiar para um provedor de backup.</span><span class="sxs-lookup"><span data-stu-id="0f575-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="0f575-153">Por exemplo, considere um aplicativo que precisa de políticas etárias personalizadas e uma recuperação de política baseada em função mais tradicional.</span><span class="sxs-lookup"><span data-stu-id="0f575-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="0f575-154">Um aplicativo desse tipo poderia usar um provedor de política de autorização personalizado que:</span><span class="sxs-lookup"><span data-stu-id="0f575-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="0f575-155">Tenta analisar nomes de política.</span><span class="sxs-lookup"><span data-stu-id="0f575-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="0f575-156">Chamadas para um provedor de política diferente (como `DefaultAuthorizationPolicyProvider`) se o nome da política não contiver uma idade.</span><span class="sxs-lookup"><span data-stu-id="0f575-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="0f575-157">O exemplo de implementação de `IAuthorizationPolicyProvider` mostrado acima pode ser atualizado para usar o `DefaultAuthorizationPolicyProvider` criando um provedor de política de backup em seu Construtor (para ser usado no caso de o nome da política não corresponder ao padrão esperado de ' Minimumment ' + age).</span><span class="sxs-lookup"><span data-stu-id="0f575-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="0f575-158">Em seguida, o método `GetPolicyAsync` pode ser atualizado para usar a `BackupPolicyProvider` em vez de retornar NULL:</span><span class="sxs-lookup"><span data-stu-id="0f575-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="0f575-159">Política padrão</span><span class="sxs-lookup"><span data-stu-id="0f575-159">Default policy</span></span>

<span data-ttu-id="0f575-160">Além de fornecer políticas de autorização nomeadas, um `IAuthorizationPolicyProvider` personalizado precisa implementar `GetDefaultPolicyAsync` para fornecer uma política de autorização para atributos de `[Authorize]` sem um nome de política especificado.</span><span class="sxs-lookup"><span data-stu-id="0f575-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="0f575-161">Em muitos casos, esse atributo de autorização requer apenas um usuário autenticado, para que você possa fazer a política necessária com uma chamada para `RequireAuthenticatedUser`:</span><span class="sxs-lookup"><span data-stu-id="0f575-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="0f575-162">Assim como em todos os aspectos de um `IAuthorizationPolicyProvider`personalizado, você pode personalizar isso, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="0f575-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="0f575-163">Em alguns casos, pode ser desejável recuperar a política padrão de um `IAuthorizationPolicyProvider`de fallback.</span><span class="sxs-lookup"><span data-stu-id="0f575-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="0f575-164">Política de fallback</span><span class="sxs-lookup"><span data-stu-id="0f575-164">Fallback policy</span></span>

<span data-ttu-id="0f575-165">Um `IAuthorizationPolicyProvider` personalizado pode, opcionalmente, implementar `GetFallbackPolicyAsync` para fornecer uma política que é usada ao [combinar políticas](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) e quando não há políticas especificadas.</span><span class="sxs-lookup"><span data-stu-id="0f575-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="0f575-166">Se `GetFallbackPolicyAsync` retornar uma política não nula, a política retornada será usada pelo middleware de autorização quando nenhuma política for especificada para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0f575-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="0f575-167">Se nenhuma política de fallback for necessária, o provedor poderá retornar `null` ou adiar para o provedor de fallback:</span><span class="sxs-lookup"><span data-stu-id="0f575-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="0f575-168">Usar um IAuthorizationPolicyProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="0f575-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="0f575-169">Para usar políticas personalizadas de um `IAuthorizationPolicyProvider`, você deve:</span><span class="sxs-lookup"><span data-stu-id="0f575-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="0f575-170">Registre os tipos de `AuthorizationHandler` apropriados com injeção de dependência (descrita em [autorização baseada em políticas](xref:security/authorization/policies#authorization-handlers)), assim como acontece com todos os cenários de autorização baseados em políticas.</span><span class="sxs-lookup"><span data-stu-id="0f575-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="0f575-171">Registre o tipo de `IAuthorizationPolicyProvider` personalizado na coleção de serviços de injeção de dependência do aplicativo (em `Startup.ConfigureServices`) para substituir o provedor de política padrão.</span><span class="sxs-lookup"><span data-stu-id="0f575-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="0f575-172">Um exemplo de `IAuthorizationPolicyProvider` personalizado completo está disponível no [repositório GitHub ASPNET/AuthSamples](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="0f575-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
