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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Provedores de política de autorização personalizados usando IAuthorizationPolicyProvider no ASP.NET Core 

Por [Mike Rousos](https://github.com/mjrousos)

Normalmente, ao usar [a autorização baseada em políticas](xref:security/authorization/policies), as políticas são `AuthorizationOptions.AddPolicy` registradas chamando como parte da configuração do serviço de autorização. Em alguns cenários, talvez não seja possível (ou desejável) registrar todas as políticas de autorização dessa maneira. Nesses casos, você pode usar um personalizado `IAuthorizationPolicyProvider` para controlar como as políticas de autorização são fornecidas.

Exemplos de cenários em que um [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado pode ser útil incluem:

* Usando um serviço externo para fornecer avaliação de política.
* Usando uma grande variedade de políticas (para diferentes números de sala ou idades, por exemplo), não faz sentido adicionar cada política de autorização individual com uma `AuthorizationOptions.AddPolicy` chamada.
* Criando políticas em tempo de execução com base nas informações de uma fonte de dados externa (como um banco de dado) ou determinando os requisitos de autorização dinamicamente por meio de outro mecanismo.

[Exiba ou baixe o código de exemplo](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) do [repositório GitHub do AspNetCore](https://github.com/aspnet/AspNetCore). Baixe o arquivo ZIP do repositório ASPNET/AspNetCore. Descompacte o arquivo. Navegue até a pasta de projeto *src/Security/Samples/CustomPolicyProvider* .

## <a name="customize-policy-retrieval"></a>Personalizar a recuperação de política

ASP.NET Core aplicativos usam uma implementação da `IAuthorizationPolicyProvider` interface para recuperar políticas de autorização. Por padrão, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) é registrado e usado. `DefaultAuthorizationPolicyProvider`Retorna as políticas do `AuthorizationOptions` fornecido em uma `IServiceCollection.AddAuthorization` chamada.

Você pode personalizar esse comportamento registrando uma implementação diferente `IAuthorizationPolicyProvider` no contêiner de injeção de [dependência](xref:fundamentals/dependency-injection) do aplicativo. 

A `IAuthorizationPolicyProvider` interface contém duas APIs:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retorna uma política de autorização para um determinado nome.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retorna a política de autorização padrão (a política usada `[Authorize]` para atributos sem uma política especificada). 

Ao implementar essas duas APIs, você pode personalizar como as políticas de autorização são fornecidas.

## <a name="parameterized-authorize-attribute-example"></a>Exemplo de atributo de autorização com parâmetros

Um cenário em `IAuthorizationPolicyProvider` que é útil é habilitar `[Authorize]` atributos personalizados cujos requisitos dependem de um parâmetro. Por exemplo, na documentação de [autorização baseada em políticas](xref:security/authorization/policies) , uma política baseada em idade ("AtLeast21") foi usada como exemplo. Se ações de controlador diferentes em um aplicativo devem ser disponibilizadas para usuários de *diferentes* idades, pode ser útil ter muitas políticas diferentes baseadas em idade. Em vez de registrar todas as diferentes políticas baseadas em idade nas `AuthorizationOptions`quais o aplicativo precisará, você pode gerar as políticas dinamicamente com um personalizado. `IAuthorizationPolicyProvider` Para facilitar o uso das políticas, você pode anotar ações com atributo de autorização personalizado `[MinimumAgeAuthorize(20)]`como.

## <a name="custom-authorization-attributes"></a>Atributos de autorização personalizados

As políticas de autorização são identificadas por seus nomes. O personalizado `MinimumAgeAuthorizeAttribute` descrito anteriormente precisa mapear argumentos em uma cadeia de caracteres que pode ser usada para recuperar a política de autorização correspondente. Você pode fazer isso derivando de `AuthorizeAttribute` e fazendo com que a `Age` Propriedade envolva `AuthorizeAttribute.Policy` a propriedade.

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

Esse tipo de atributo tem `Policy` uma cadeia de caracteres baseada no prefixo embutido em`"MinimumAge"`código () e um inteiro passado por meio do construtor.

Você pode aplicá-lo a ações da mesma maneira que outros `Authorize` atributos, exceto pelo fato de que ele usa um inteiro como um parâmetro.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>IAuthorizationPolicyProvider personalizado

O personalizado `MinimumAgeAuthorizeAttribute` facilita a solicitação de políticas de autorização para qualquer idade mínima desejada. O próximo problema a ser resolvido é garantir que as políticas de autorização estejam disponíveis para todas essas idades diferentes. É aí que um `IAuthorizationPolicyProvider` é útil.

Ao usar `MinimumAgeAuthorizationAttribute`o, os nomes de política de autorização seguirão o padrão `"MinimumAge" + Age`, `IAuthorizationPolicyProvider` de modo que o personalizado deve gerar políticas de autorização:

* Analisando a idade do nome da política.
* Usando `AuthorizationPolicyBuilder` para criar um novo`AuthorizationPolicy`
* Neste e nos exemplos a seguir, supõe-se que o usuário é autenticado por meio de um cookie. O `AuthorizationPolicyBuilder` deve ser construído com pelo menos um nome de esquema de autorização ou sempre com sucesso. Caso contrário, não há informações sobre como fornecer um desafio ao usuário e uma exceção será lançada.
* Adição de requisitos à política com base na idade com `AuthorizationPolicyBuilder.AddRequirements`. Em outros cenários, você pode usar `RequireClaim`, `RequireRole`ou `RequireUserName` em vez disso.

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

## <a name="multiple-authorization-policy-providers"></a>Vários provedores de política de autorização

Ao usar implementações personalizadas `IAuthorizationPolicyProvider` , tenha em mente que ASP.NET Core usa apenas uma instância do. `IAuthorizationPolicyProvider` Se um provedor personalizado não puder fornecer políticas de autorização para todos os nomes de política que serão usados, ele deverá retornar a um provedor de backup. 

Por exemplo, considere um aplicativo que precisa de políticas etárias personalizadas e uma recuperação de política baseada em função mais tradicional. Um aplicativo desse tipo poderia usar um provedor de política de autorização personalizado que:

* Tenta analisar nomes de política. 
* Chama um provedor de política diferente (como `DefaultAuthorizationPolicyProvider`) se o nome da política não contiver uma idade.

A implementação `IAuthorizationPolicyProvider` de exemplo mostrada acima pode ser atualizada para `DefaultAuthorizationPolicyProvider` usar o criando um provedor de política de fallback em seu Construtor (para ser usado no caso de o nome da política não corresponder ao padrão esperado de ' minimumment ' + age).

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Em seguida, `GetPolicyAsync` o método pode ser atualizado para usar `FallbackPolicyProvider` o em vez de retornar NULL:

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Política padrão

Além de fornecer políticas de autorização nomeadas, é `IAuthorizationPolicyProvider` necessário implementar `GetDefaultPolicyAsync` um personalizado para fornecer uma política de `[Authorize]` autorização para atributos sem um nome de política especificado.

Em muitos casos, esse atributo de autorização requer apenas um usuário autenticado, para que você possa fazer a política necessária com uma `RequireAuthenticatedUser`chamada para:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Assim como com todos os aspectos de `IAuthorizationPolicyProvider`um personalizado, você pode personalizar isso, conforme necessário. Em alguns casos, pode ser desejável recuperar a política padrão de um fallback `IAuthorizationPolicyProvider`.

## <a name="required-policy"></a>Política necessária

Um personalizado `IAuthorizationPolicyProvider` precisa implementar `GetRequiredPolicyAsync` para, opcionalmente, fornecer uma política que seja sempre necessária. Se `GetRequiredPolicyAsync` o retornar uma política não nula, essa política será combinada com qualquer outra política (nomeada ou padrão) solicitada.

Se nenhuma política necessária for necessária, o provedor poderá simplesmente retornar nulo ou adiar para o provedor de fallback:

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Usar um IAuthorizationPolicyProvider personalizado

Para usar políticas personalizadas de um `IAuthorizationPolicyProvider`, você deve:

* Registre os tipos `AuthorizationHandler` apropriados com injeção de dependência (descrita em [autorização baseada em políticas](xref:security/authorization/policies#authorization-handlers)), assim como acontece com todos os cenários de autorização baseados em políticas.
* Registre o tipo `IAuthorizationPolicyProvider` personalizado na coleção de serviços de injeção de dependência do aplicativo `Startup.ConfigureServices`(em) para substituir o provedor de política padrão.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Uma amostra personalizada `IAuthorizationPolicyProvider` completa está disponível no [repositório GitHub ASPNET/AuthSamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).
