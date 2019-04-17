---
title: Provedores de política de autorização personalizada no ASP.NET Core
author: mjrousos
description: Saiba como usar um IAuthorizationPolicyProvider personalizado em um aplicativo ASP.NET Core para gerar dinamicamente a políticas de autorização.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: e17372bb0ec9091c385a70b1e907eaa3cff24003
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614403"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Provedores de política de autorização personalizados usando IAuthorizationPolicyProvider no ASP.NET Core 

Por [Mike Rousos](https://github.com/mjrousos)

Normalmente ao usar [baseado em políticas de autorização](xref:security/authorization/policies), as políticas são registradas por meio da chamada `AuthorizationOptions.AddPolicy` como parte da configuração do serviço de autorização. Em alguns cenários, pode não ser possível (ou desejável) para registrar todas as políticas de autorização dessa maneira. Nesses casos, você pode usar um personalizado `IAuthorizationPolicyProvider` para controlar como as políticas de autorização são fornecidas.

Exemplos de cenários onde um personalizado [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) podem ser úteis incluem:

* Usando um serviço externo para fornecer a avaliação da política.
* Usando uma grande variedade de diretivas (para números de sala de diferentes ou com as idades, por exemplo), portanto, não faz sentido adicionar cada política de autorização individuais com um `AuthorizationOptions.AddPolicy` chamar.
* Criação de políticas em tempo de execução com base nas informações em uma fonte de dados externa (como um banco de dados) ou determinar os requisitos de autorização dinamicamente por meio de outro mecanismo.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) do [repositório GitHub de AspNetCore](https://github.com/aspnet/AspNetCore). Baixe o arquivo ZIP do repositório aspnet/AspNetCore. Descompacte o arquivo. Navegue até a *src/Security/samples/CustomPolicyProvider* pasta do projeto.

## <a name="customize-policy-retrieval"></a>Personalizar a recuperação da política

Aplicativos ASP.NET Core usam uma implementação do `IAuthorizationPolicyProvider` interface para recuperar as políticas de autorização. Por padrão, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registrado e usado. `DefaultAuthorizationPolicyProvider` Retorna as políticas do `AuthorizationOptions` fornecidas em um `IServiceCollection.AddAuthorization` chamar.

Você pode personalizar esse comportamento, registrando um diferentes `IAuthorizationPolicyProvider` implementação do aplicativo [injeção de dependência](xref:fundamentals/dependency-injection) contêiner. 

O `IAuthorizationPolicyProvider` interface contém duas APIs:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retorna uma política de autorização para um determinado nome.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retorna a política de autorização padrão (a política usada para `[Authorize]` atributos sem uma política especificada). 

Implementando essas duas APIs, você pode personalizar como as políticas de autorização são fornecidas.

## <a name="parameterized-authorize-attribute-example"></a>Parametrizadas autorizar o exemplo de atributo

Um cenário em que `IAuthorizationPolicyProvider` é útil é a habilitação do personalizado `[Authorize]` atributos cujos requisitos dependem de um parâmetro. Por exemplo, na [autorização baseada em política](xref:security/authorization/policies) documentação, uma com base em idade ("AtLeast21") política foi usada como um exemplo. Se as ações de controlador diferente em um aplicativo devem ser disponibilizadas para os usuários do *diferentes* há séculos, pode ser útil ter muitas políticas diferentes com base em idade. Em vez de registrar todas as diferentes com base em idade políticas que o aplicativo será necessário em `AuthorizationOptions`, você pode gerar as políticas dinamicamente com um personalizado `IAuthorizationPolicyProvider`. Para tornar o uso de políticas mais fácil, você pode anotar as ações com o atributo de autorização personalizado, como `[MinimumAgeAuthorize(20)]`.

## <a name="custom-authorization-attributes"></a>Atributos de autorização personalizados

Políticas de autorização são identificadas por seus nomes. Personalizado `MinimumAgeAuthorizeAttribute` descrito anteriormente, precisa mapear argumentos em uma cadeia de caracteres que pode ser usada para recuperar a política de autorização correspondente. Você pode fazer isso, derivando de `AuthorizeAttribute` e fazer a `Age` quebra automática de propriedade a `AuthorizeAttribute.Policy` propriedade.

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

Esse tipo de atributo tem um `Policy` cadeia de caracteres com base no prefixo embutido em código (`"MinimumAge"`) e um número inteiro passado por meio do construtor.

Você pode aplicá-lo às ações da mesma maneira que outras `Authorize` atributos, exceto que assume um inteiro como um parâmetro.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>IAuthorizationPolicyProvider personalizado

Personalizado `MinimumAgeAuthorizeAttribute` facilita a políticas de autorização da solicitação para qualquer idade mínima desejada. O próximo problema a resolver é verificar se as políticas de autorização estão disponíveis para todas as idades diferentes. É aí que um `IAuthorizationPolicyProvider` é útil.

Ao usar `MinimumAgeAuthorizationAttribute`, os nomes de política de autorização seguirá o padrão `"MinimumAge" + Age`, então personalizado `IAuthorizationPolicyProvider` deve gerar diretivas de autorização por:

* A idade do nome da política de análise.
* Usando `AuthorizationPolicyBuilder` para criar um novo `AuthorizationPolicy`
* Adicionar requisitos para a política com base na idade com `AuthorizationPolicyBuilder.AddRequirements`. Em outros cenários, você pode usar `RequireClaim`, `RequireRole`, ou `RequireUserName` em vez disso.

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

## <a name="multiple-authorization-policy-providers"></a>Vários provedores de política de autorização

Quando usar custom `IAuthorizationPolicyProvider` implementações, tenha em mente que o ASP.NET Core usa apenas uma instância de `IAuthorizationPolicyProvider`. Se um provedor personalizado não é capaz de fornecer políticas de autorização para todos os nomes de política que serão usados, ele deve fazer fallback para um provedor de backup. 

Por exemplo, considere um aplicativo que precisa de políticas personalizadas de idade e recuperação de política baseada em função mais tradicional. Esse aplicativo poderia usar um provedor de diretivas de autorização personalizada que:

* Tenta analisar nomes de política. 
* Chamadas para um provedor de política diferente (como `DefaultAuthorizationPolicyProvider`) se o nome da política não contiver uma idade.

O exemplo `IAuthorizationPolicyProvider` implementação mostrada acima pode ser atualizada para usar o `DefaultAuthorizationPolicyProvider` , criando um provedor de política de fallback em seu construtor (a ser usado no caso do nome da política não corresponde ao seu padrão esperado de 'MinimumAge' + idade).

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Em seguida, o `GetPolicyAsync` método pode ser atualizado para usar o `FallbackPolicyProvider` em vez de retornar null:

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Política padrão

Além de fornecer políticas de autorização nomeado, um personalizado `IAuthorizationPolicyProvider` precisa implementar `GetDefaultPolicyAsync` para fornecer uma política de autorização para `[Authorize]` atributos sem um nome de política especificado.

Em muitos casos, esse atributo de autorização requer apenas um usuário autenticado, portanto, você pode fazer a diretiva necessária com uma chamada para `RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

Assim como acontece com todos os aspectos de um personalizado `IAuthorizationPolicyProvider`, você pode personalizá-lo, conforme necessário. Em alguns casos, ele pode ser desejável para recuperar a política padrão de fallback `IAuthorizationPolicyProvider`.

## <a name="required-policy"></a>Diretiva necessária

Um personalizado `IAuthorizationPolicyProvider` precisa implementar `GetRequiredPolicyAsync` para, opcionalmente, forneça uma política que é sempre necessária. Se `GetRequiredPolicyAsync` retorna uma política de não-nulo, essa política será combinada com qualquer outro (nomeado ou padrão) política que é solicitada.

Se nenhuma política necessária for necessária, o provedor poderá simplesmente retornar nulo ou adiar para o provedor de fallback:

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Use um IAuthorizationPolicyProvider personalizado

Para usar políticas personalizadas de um `IAuthorizationPolicyProvider`, você deve:

* Registrar apropriado `AuthorizationHandler` tipos de injeção de dependência (descrito na [autorização baseada em política](xref:security/authorization/policies#authorization-handlers)), assim como acontece com todos os cenários de autorização baseada em política.
* Registrar personalizado `IAuthorizationPolicyProvider` tipo na coleção de serviço de injeção de dependência do aplicativo (em `Startup.ConfigureServices`) para substituir o provedor de política padrão.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Um personalizado completo `IAuthorizationPolicyProvider` exemplo está disponível na [repositório do GitHub aspnet/AuthSamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).
