---
title: Autorização baseada em declarações no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar verificações de declarações para autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: 6b60ae5515819b017ab577f655ed91ee4d8ed0dd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086148"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Autorização baseada em declarações no ASP.NET Core

<a name="security-authorization-claims-based"></a>

Quando uma identidade é criada ele pode ser atribuído um ou mais declarações emitidas por uma parte confiável. Uma declaração é um par nome-valor que representa o que o assunto é, não o que o assunto pode fazer. Por exemplo, você pode ter de motorista uma carteira, emitida por uma autoridade de licença de condução local. Sua carteira de motorista tem sua data de nascimento. Nesse caso, seria o nome da declaração `DateOfBirth`, o valor da declaração seria sua data de nascimento, por exemplo `8th June 1970` e o emissor seria a condução autoridade de licença. Autorização baseada em declarações, em sua forma mais simples, verifica o valor de uma declaração e permite o acesso a um recurso com base no valor. Por exemplo, se você quiser que o processo de autorização de acesso para um clube de noite pode ser:

O Diretor de segurança de porta seria avaliado o valor da sua data de nascimento de declaração e se elas têm confiança o emissor (a autoridade de licença condução) antes de conceder que acesso a você.

Uma identidade pode conter várias declarações com vários valores e pode conter várias declarações do mesmo tipo.

## <a name="adding-claims-checks"></a>Adição de verificações de declarações

Declaração de verificações de autorização com base são declarativas – o desenvolvedor incorpora-los dentro de seu código, em relação a um controlador ou uma ação dentro de um controlador, especificando as declarações que o usuário atual deve ter e, opcionalmente, o valor da declaração deve conter para acessar o recurso solicitado. Declarações de requisitos são baseada em política, o desenvolvedor deve criar e registrar uma política de expressar os requisitos de declarações.

O tipo mais simples de política procura a presença de uma declaração de declaração e não verifica o valor.

Primeiro, você precisa compilar e registrar a política. Isso ocorre como parte da configuração do serviço de autorização, que normalmente faz parte do `ConfigureServices()` em seu *Startup.cs* arquivo.

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

Nesse caso, o `EmployeeOnly` política verifica a presença de um `EmployeeNumber` a identidade atual de declaração.

Em seguida, aplicar a política usando o `Policy` propriedade no `AuthorizeAttribute` atributo para especificar o nome da política;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

O `AuthorizeAttribute` atributo pode ser aplicado a um controlador inteiro, nesta instância, somente as identidades a política de correspondência terá permissão de acesso a qualquer ação no controlador.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Se você tiver um controlador que é protegido pela `AuthorizeAttribute` de atributo, mas deseja permitir acesso anônimo a ações específicas que você aplicar o `AllowAnonymousAttribute` atributo.

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

A maioria das declarações vêm com um valor. Você pode especificar uma lista de valores permitido ao criar a política. O exemplo a seguir teria êxito apenas para os funcionários cujo número de funcionário foi 1, 2, 3, 4 ou 5.

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

### <a name="add-a-generic-claim-check"></a>Adicionar uma verificação de declaração genérica

Se o valor da declaração não é um único valor ou uma transformação é necessária, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Para obter mais informações, consulte [usando um func para atender a uma política de](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Avaliação de política múltipla

Se você aplicar várias políticas para um controlador ou ação, todas as políticas devem passar antes de conceder acesso. Por exemplo:

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

No exemplo acima qualquer identidade que atende a `EmployeeOnly` diretiva pode acessar o `Payslip` ação como essa política é imposta no controlador. No entanto para chamar o `UpdateSalary` ação de identidade deve ser atendidos *ambos* o `EmployeeOnly` política e o `HumanResources` política.

Se você quiser políticas mais complicadas, como assumir uma data de nascimento de declaração, calculando uma idade dele, em seguida, verificando a idade é 21 ou mais antigo e em seguida, você precisa escrever [manipuladores de política personalizada](xref:security/authorization/policies).
