---
title: Provedores de política de autorização personalizada no ASP.NET Core
author: mjrousos
description: Saiba como usar um IAuthorizationPolicyProvider personalizado em um aplicativo ASP.NET Core para gerar dinamicamente políticas de autorização.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: b11f7f94e1042e65d5f2ff8ab0fe9ea7838bebeb
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108055"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="49072-103">Provedores de política de autorização personalizados usando IAuthorizationPolicyProvider no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49072-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="49072-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="49072-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="49072-105">Normalmente, ao usar [a autorização baseada em políticas](xref:security/authorization/policies), as políticas são `AuthorizationOptions.AddPolicy` registradas chamando como parte da configuração do serviço de autorização.</span><span class="sxs-lookup"><span data-stu-id="49072-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="49072-106">Em alguns cenários, talvez não seja possível (ou desejável) registrar todas as políticas de autorização dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="49072-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="49072-107">Nesses casos, você pode usar um personalizado `IAuthorizationPolicyProvider` para controlar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="49072-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="49072-108">Exemplos de cenários em que um [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado pode ser útil incluem:</span><span class="sxs-lookup"><span data-stu-id="49072-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="49072-109">Usando um serviço externo para fornecer avaliação de política.</span><span class="sxs-lookup"><span data-stu-id="49072-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="49072-110">Usando uma grande variedade de políticas (para diferentes números de sala ou idades, por exemplo), não faz sentido adicionar cada política de autorização individual com uma `AuthorizationOptions.AddPolicy` chamada.</span><span class="sxs-lookup"><span data-stu-id="49072-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="49072-111">Criando políticas em tempo de execução com base nas informações de uma fonte de dados externa (como um banco de dado) ou determinando os requisitos de autorização dinamicamente por meio de outro mecanismo.</span><span class="sxs-lookup"><span data-stu-id="49072-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="49072-112">[Exiba ou baixe o código de exemplo](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) do [repositório GitHub do AspNetCore](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="49072-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="49072-113">Baixe o arquivo ZIP do repositório ASPNET/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="49072-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="49072-114">Descompacte o arquivo.</span><span class="sxs-lookup"><span data-stu-id="49072-114">Unzip the file.</span></span> <span data-ttu-id="49072-115">Navegue até a pasta de projeto *src/Security/Samples/CustomPolicyProvider* .</span><span class="sxs-lookup"><span data-stu-id="49072-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="49072-116">Personalizar a recuperação de política</span><span class="sxs-lookup"><span data-stu-id="49072-116">Customize policy retrieval</span></span>

<span data-ttu-id="49072-117">ASP.NET Core aplicativos usam uma implementação da `IAuthorizationPolicyProvider` interface para recuperar políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="49072-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="49072-118">Por padrão, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) é registrado e usado.</span><span class="sxs-lookup"><span data-stu-id="49072-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="49072-119">`DefaultAuthorizationPolicyProvider`Retorna as políticas do `AuthorizationOptions` fornecido em uma `IServiceCollection.AddAuthorization` chamada.</span><span class="sxs-lookup"><span data-stu-id="49072-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="49072-120">Você pode personalizar esse comportamento registrando uma implementação diferente `IAuthorizationPolicyProvider` no contêiner de injeção de [dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="49072-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="49072-121">A `IAuthorizationPolicyProvider` interface contém duas APIs:</span><span class="sxs-lookup"><span data-stu-id="49072-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="49072-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retorna uma política de autorização para um determinado nome.</span><span class="sxs-lookup"><span data-stu-id="49072-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="49072-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retorna a política de autorização padrão (a política usada `[Authorize]` para atributos sem uma política especificada).</span><span class="sxs-lookup"><span data-stu-id="49072-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="49072-124">Ao implementar essas duas APIs, você pode personalizar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="49072-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="49072-125">Exemplo de atributo de autorização com parâmetros</span><span class="sxs-lookup"><span data-stu-id="49072-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="49072-126">Um cenário em `IAuthorizationPolicyProvider` que é útil é habilitar `[Authorize]` atributos personalizados cujos requisitos dependem de um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="49072-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="49072-127">Por exemplo, na documentação de [autorização baseada em políticas](xref:security/authorization/policies) , uma política baseada em idade ("AtLeast21") foi usada como exemplo.</span><span class="sxs-lookup"><span data-stu-id="49072-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="49072-128">Se ações de controlador diferentes em um aplicativo devem ser disponibilizadas para usuários de *diferentes* idades, pode ser útil ter muitas políticas diferentes baseadas em idade.</span><span class="sxs-lookup"><span data-stu-id="49072-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="49072-129">Em vez de registrar todas as diferentes políticas baseadas em idade nas `AuthorizationOptions`quais o aplicativo precisará, você pode gerar as políticas dinamicamente com um personalizado. `IAuthorizationPolicyProvider`</span><span class="sxs-lookup"><span data-stu-id="49072-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="49072-130">Para facilitar o uso das políticas, você pode anotar ações com atributo de autorização personalizado `[MinimumAgeAuthorize(20)]`como.</span><span class="sxs-lookup"><span data-stu-id="49072-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="49072-131">Atributos de autorização personalizados</span><span class="sxs-lookup"><span data-stu-id="49072-131">Custom Authorization attributes</span></span>

<span data-ttu-id="49072-132">As políticas de autorização são identificadas por seus nomes.</span><span class="sxs-lookup"><span data-stu-id="49072-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="49072-133">O personalizado `MinimumAgeAuthorizeAttribute` descrito anteriormente precisa mapear argumentos em uma cadeia de caracteres que pode ser usada para recuperar a política de autorização correspondente.</span><span class="sxs-lookup"><span data-stu-id="49072-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="49072-134">Você pode fazer isso derivando de `AuthorizeAttribute` e fazendo com que a `Age` Propriedade envolva `AuthorizeAttribute.Policy` a propriedade.</span><span class="sxs-lookup"><span data-stu-id="49072-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="49072-135">Esse tipo de atributo tem `Policy` uma cadeia de caracteres baseada no prefixo embutido em`"MinimumAge"`código () e um inteiro passado por meio do construtor.</span><span class="sxs-lookup"><span data-stu-id="49072-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="49072-136">Você pode aplicá-lo a ações da mesma maneira que outros `Authorize` atributos, exceto pelo fato de que ele usa um inteiro como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="49072-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="49072-137">IAuthorizationPolicyProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="49072-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="49072-138">O personalizado `MinimumAgeAuthorizeAttribute` facilita a solicitação de políticas de autorização para qualquer idade mínima desejada.</span><span class="sxs-lookup"><span data-stu-id="49072-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="49072-139">O próximo problema a ser resolvido é garantir que as políticas de autorização estejam disponíveis para todas essas idades diferentes.</span><span class="sxs-lookup"><span data-stu-id="49072-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="49072-140">É aí que um `IAuthorizationPolicyProvider` é útil.</span><span class="sxs-lookup"><span data-stu-id="49072-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="49072-141">Ao usar `MinimumAgeAuthorizationAttribute`o, os nomes de política de autorização seguirão o padrão `"MinimumAge" + Age`, `IAuthorizationPolicyProvider` de modo que o personalizado deve gerar políticas de autorização:</span><span class="sxs-lookup"><span data-stu-id="49072-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="49072-142">Analisando a idade do nome da política.</span><span class="sxs-lookup"><span data-stu-id="49072-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="49072-143">Usando `AuthorizationPolicyBuilder` para criar um novo`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="49072-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="49072-144">Neste e nos exemplos a seguir, supõe-se que o usuário é autenticado por meio de um cookie.</span><span class="sxs-lookup"><span data-stu-id="49072-144">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="49072-145">O `AuthorizationPolicyBuilder` deve ser construído com pelo menos um nome de esquema de autorização ou sempre com sucesso.</span><span class="sxs-lookup"><span data-stu-id="49072-145">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="49072-146">Caso contrário, não há informações sobre como fornecer um desafio ao usuário e uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="49072-146">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="49072-147">Adição de requisitos à política com base na idade com `AuthorizationPolicyBuilder.AddRequirements`.</span><span class="sxs-lookup"><span data-stu-id="49072-147">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="49072-148">Em outros cenários, você pode usar `RequireClaim`, `RequireRole`ou `RequireUserName` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="49072-148">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="49072-149">Vários provedores de política de autorização</span><span class="sxs-lookup"><span data-stu-id="49072-149">Multiple authorization policy providers</span></span>

<span data-ttu-id="49072-150">Ao usar implementações personalizadas `IAuthorizationPolicyProvider` , tenha em mente que ASP.NET Core usa apenas uma instância do. `IAuthorizationPolicyProvider`</span><span class="sxs-lookup"><span data-stu-id="49072-150">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="49072-151">Se um provedor personalizado não puder fornecer políticas de autorização para todos os nomes de política que serão usados, ele deverá retornar a um provedor de backup.</span><span class="sxs-lookup"><span data-stu-id="49072-151">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should fall back to a backup provider.</span></span> 

<span data-ttu-id="49072-152">Por exemplo, considere um aplicativo que precisa de políticas etárias personalizadas e uma recuperação de política baseada em função mais tradicional.</span><span class="sxs-lookup"><span data-stu-id="49072-152">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="49072-153">Um aplicativo desse tipo poderia usar um provedor de política de autorização personalizado que:</span><span class="sxs-lookup"><span data-stu-id="49072-153">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="49072-154">Tenta analisar nomes de política.</span><span class="sxs-lookup"><span data-stu-id="49072-154">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="49072-155">Chama um provedor de política diferente (como `DefaultAuthorizationPolicyProvider`) se o nome da política não contiver uma idade.</span><span class="sxs-lookup"><span data-stu-id="49072-155">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="49072-156">A implementação `IAuthorizationPolicyProvider` de exemplo mostrada acima pode ser atualizada para `DefaultAuthorizationPolicyProvider` usar o criando um provedor de política de fallback em seu Construtor (para ser usado no caso de o nome da política não corresponder ao padrão esperado de ' minimumment ' + age).</span><span class="sxs-lookup"><span data-stu-id="49072-156">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a fallback policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="49072-157">Em seguida, `GetPolicyAsync` o método pode ser atualizado para usar `FallbackPolicyProvider` o em vez de retornar NULL:</span><span class="sxs-lookup"><span data-stu-id="49072-157">Then, the `GetPolicyAsync` method can be updated to use the `FallbackPolicyProvider` instead of returning null:</span></span>

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="49072-158">Política padrão</span><span class="sxs-lookup"><span data-stu-id="49072-158">Default policy</span></span>

<span data-ttu-id="49072-159">Além de fornecer políticas de autorização nomeadas, é `IAuthorizationPolicyProvider` necessário implementar `GetDefaultPolicyAsync` um personalizado para fornecer uma política de `[Authorize]` autorização para atributos sem um nome de política especificado.</span><span class="sxs-lookup"><span data-stu-id="49072-159">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="49072-160">Em muitos casos, esse atributo de autorização requer apenas um usuário autenticado, para que você possa fazer a política necessária com uma `RequireAuthenticatedUser`chamada para:</span><span class="sxs-lookup"><span data-stu-id="49072-160">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="49072-161">Assim como com todos os aspectos de `IAuthorizationPolicyProvider`um personalizado, você pode personalizar isso, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="49072-161">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="49072-162">Em alguns casos, pode ser desejável recuperar a política padrão de um fallback `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="49072-162">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="required-policy"></a><span data-ttu-id="49072-163">Política necessária</span><span class="sxs-lookup"><span data-stu-id="49072-163">Required policy</span></span>

<span data-ttu-id="49072-164">Um personalizado `IAuthorizationPolicyProvider` precisa implementar `GetRequiredPolicyAsync` para, opcionalmente, fornecer uma política que seja sempre necessária.</span><span class="sxs-lookup"><span data-stu-id="49072-164">A custom `IAuthorizationPolicyProvider` needs to implement `GetRequiredPolicyAsync` to, optionally, provide a policy that is always required.</span></span> <span data-ttu-id="49072-165">Se `GetRequiredPolicyAsync` o retornar uma política não nula, essa política será combinada com qualquer outra política (nomeada ou padrão) solicitada.</span><span class="sxs-lookup"><span data-stu-id="49072-165">If `GetRequiredPolicyAsync` returns a non-null policy, that policy will be combined with any other (named or default) policy that is requested.</span></span>

<span data-ttu-id="49072-166">Se nenhuma política necessária for necessária, o provedor poderá simplesmente retornar nulo ou adiar para o provedor de fallback:</span><span class="sxs-lookup"><span data-stu-id="49072-166">If no required policy is needed, the provider can just return null or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="49072-167">Usar um IAuthorizationPolicyProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="49072-167">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="49072-168">Para usar políticas personalizadas de um `IAuthorizationPolicyProvider`, você deve:</span><span class="sxs-lookup"><span data-stu-id="49072-168">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="49072-169">Registre os tipos `AuthorizationHandler` apropriados com injeção de dependência (descrita em [autorização baseada em políticas](xref:security/authorization/policies#authorization-handlers)), assim como acontece com todos os cenários de autorização baseados em políticas.</span><span class="sxs-lookup"><span data-stu-id="49072-169">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="49072-170">Registre o tipo `IAuthorizationPolicyProvider` personalizado na coleção de serviços de injeção de dependência do aplicativo `Startup.ConfigureServices`(em) para substituir o provedor de política padrão.</span><span class="sxs-lookup"><span data-stu-id="49072-170">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="49072-171">Uma amostra personalizada `IAuthorizationPolicyProvider` completa está disponível no [repositório GitHub ASPNET/AuthSamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="49072-171">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
