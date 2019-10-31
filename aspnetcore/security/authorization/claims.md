---
title: Autorização baseada em declarações no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar verificações de declarações para autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: e289851aafcbc7e3b3f60ab9fbe4b182a78bdf8a
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143436"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Autorização baseada em declarações no ASP.NET Core

<a name="security-authorization-claims-based"></a>

Quando uma identidade é criada, ela pode ser atribuída a uma ou mais declarações emitidas por uma parte confiável. Uma declaração é um par de valor de nome que representa qual é o assunto, não o que o assunto pode fazer. Por exemplo, você pode ter uma licença de driver, emitida por uma autoridade de licença de condução local. A licença do seu driver tem sua data de nascimento. Nesse caso, o nome da declaração seria `DateOfBirth`, o valor da declaração seria a sua data de nascimento, por exemplo `8th June 1970` e o emissor seria a autoridade de licença de condução. A autorização baseada em declarações, em sua mais simples, verifica o valor de uma declaração e permite o acesso a um recurso com base no valor. Por exemplo, se você quiser acesso a um clube noturno, o processo de autorização poderá ser:

O diretor de segurança de porta avaliaria o valor de sua declaração de data de nascimento e se confiará no emissor (a autoridade de licença de condução) antes de conceder acesso.

Uma identidade pode conter várias declarações com vários valores e pode conter várias declarações do mesmo tipo.

## <a name="adding-claims-checks"></a>Adicionando verificações de declarações

As verificações de autorização baseadas em declarações são declarativas-o desenvolvedor as incorpora em seu código, em um controlador ou em uma ação dentro de um controlador, especificando as declarações que o usuário atual deve possuir e, opcionalmente, o valor que a declaração deve manter para acessar o recurso solicitado. Os requisitos de declarações são baseados em políticas, o desenvolvedor deve criar e registrar uma política que expresse os requisitos de declarações.

O tipo mais simples de política de declaração procura a presença de uma declaração e não verifica o valor.

Primeiro, você precisa criar e registrar a política. Isso ocorre como parte da configuração do serviço de autorização, que normalmente faz parte do `ConfigureServices()` no arquivo *Startup.cs* .

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

Nesse caso, a política de `EmployeeOnly` verifica a presença de uma declaração de `EmployeeNumber` na identidade atual.

Em seguida, aplique a política usando a propriedade `Policy` no atributo `AuthorizeAttribute` para especificar o nome da política;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

O atributo `AuthorizeAttribute` pode ser aplicado a um controlador inteiro, nessa instância, somente as identidades correspondentes à política terão permissão para acessar qualquer ação no controlador.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Se você tiver um controlador protegido pelo atributo `AuthorizeAttribute`, mas quiser permitir acesso anônimo a determinadas ações, aplique o atributo `AllowAnonymousAttribute`.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

A maioria das declarações vem com um valor. Você pode especificar uma lista de valores permitidos ao criar a política. O exemplo a seguir só terá êxito para funcionários cujo número de funcionário fosse 1, 2, 3, 4 ou 5.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Adicionar uma verificação de declaração genérica

Se o valor da declaração não for um valor único ou uma transformação for necessária, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Para obter mais informações, consulte [usando um Func para atender a uma política](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Avaliação de várias políticas

Se você aplicar várias políticas a um controlador ou uma ação, todas as políticas deverão passar antes que o acesso seja concedido. Por exemplo:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

No exemplo acima, qualquer identidade que atenda à política de `EmployeeOnly` pode acessar a ação `Payslip`, pois essa política é imposta no controlador. No entanto, para chamar a ação de `UpdateSalary`, a identidade deve atender *tanto* à política de `EmployeeOnly` quanto à política de `HumanResources`.

Se você quiser políticas mais complicadas, como a obtenção de uma declaração de data de nascimento, calcular uma idade a partir dela, verificando se a idade é 21 ou mais antiga, você precisa escrever [manipuladores de política personalizados](xref:security/authorization/policies).
