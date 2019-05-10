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
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="7d74b-103">Autorização baseada em declarações no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d74b-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="7d74b-104">Quando uma identidade é criada ele pode ser atribuído um ou mais declarações emitidas por uma parte confiável.</span><span class="sxs-lookup"><span data-stu-id="7d74b-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="7d74b-105">Uma declaração é um par nome-valor que representa o que o assunto é, não o que o assunto pode fazer.</span><span class="sxs-lookup"><span data-stu-id="7d74b-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="7d74b-106">Por exemplo, você pode ter de motorista uma carteira, emitida por uma autoridade de licença de condução local.</span><span class="sxs-lookup"><span data-stu-id="7d74b-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="7d74b-107">Sua carteira de motorista tem sua data de nascimento.</span><span class="sxs-lookup"><span data-stu-id="7d74b-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="7d74b-108">Nesse caso, seria o nome da declaração `DateOfBirth`, o valor da declaração seria sua data de nascimento, por exemplo `8th June 1970` e o emissor seria a condução autoridade de licença.</span><span class="sxs-lookup"><span data-stu-id="7d74b-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="7d74b-109">Autorização baseada em declarações, em sua forma mais simples, verifica o valor de uma declaração e permite o acesso a um recurso com base no valor.</span><span class="sxs-lookup"><span data-stu-id="7d74b-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="7d74b-110">Por exemplo, se você quiser que o processo de autorização de acesso para um clube de noite pode ser:</span><span class="sxs-lookup"><span data-stu-id="7d74b-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="7d74b-111">O Diretor de segurança de porta seria avaliado o valor da sua data de nascimento de declaração e se elas têm confiança o emissor (a autoridade de licença condução) antes de conceder que acesso a você.</span><span class="sxs-lookup"><span data-stu-id="7d74b-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="7d74b-112">Uma identidade pode conter várias declarações com vários valores e pode conter várias declarações do mesmo tipo.</span><span class="sxs-lookup"><span data-stu-id="7d74b-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="7d74b-113">Adição de verificações de declarações</span><span class="sxs-lookup"><span data-stu-id="7d74b-113">Adding claims checks</span></span>

<span data-ttu-id="7d74b-114">Declaração de verificações de autorização com base são declarativas – o desenvolvedor incorpora-los dentro de seu código, em relação a um controlador ou uma ação dentro de um controlador, especificando as declarações que o usuário atual deve ter e, opcionalmente, o valor da declaração deve conter para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="7d74b-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="7d74b-115">Declarações de requisitos são baseada em política, o desenvolvedor deve criar e registrar uma política de expressar os requisitos de declarações.</span><span class="sxs-lookup"><span data-stu-id="7d74b-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="7d74b-116">O tipo mais simples de política procura a presença de uma declaração de declaração e não verifica o valor.</span><span class="sxs-lookup"><span data-stu-id="7d74b-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="7d74b-117">Primeiro, você precisa compilar e registrar a política.</span><span class="sxs-lookup"><span data-stu-id="7d74b-117">First you need to build and register the policy.</span></span> <span data-ttu-id="7d74b-118">Isso ocorre como parte da configuração do serviço de autorização, que normalmente faz parte do `ConfigureServices()` em seu *Startup.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7d74b-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="7d74b-119">Nesse caso, o `EmployeeOnly` política verifica a presença de um `EmployeeNumber` a identidade atual de declaração.</span><span class="sxs-lookup"><span data-stu-id="7d74b-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="7d74b-120">Em seguida, aplicar a política usando o `Policy` propriedade no `AuthorizeAttribute` atributo para especificar o nome da política;</span><span class="sxs-lookup"><span data-stu-id="7d74b-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="7d74b-121">O `AuthorizeAttribute` atributo pode ser aplicado a um controlador inteiro, nesta instância, somente as identidades a política de correspondência terá permissão de acesso a qualquer ação no controlador.</span><span class="sxs-lookup"><span data-stu-id="7d74b-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="7d74b-122">Se você tiver um controlador que é protegido pela `AuthorizeAttribute` de atributo, mas deseja permitir acesso anônimo a ações específicas que você aplicar o `AllowAnonymousAttribute` atributo.</span><span class="sxs-lookup"><span data-stu-id="7d74b-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="7d74b-123">A maioria das declarações vêm com um valor.</span><span class="sxs-lookup"><span data-stu-id="7d74b-123">Most claims come with a value.</span></span> <span data-ttu-id="7d74b-124">Você pode especificar uma lista de valores permitido ao criar a política.</span><span class="sxs-lookup"><span data-stu-id="7d74b-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="7d74b-125">O exemplo a seguir teria êxito apenas para os funcionários cujo número de funcionário foi 1, 2, 3, 4 ou 5.</span><span class="sxs-lookup"><span data-stu-id="7d74b-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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

### <a name="add-a-generic-claim-check"></a><span data-ttu-id="7d74b-126">Adicionar uma verificação de declaração genérica</span><span class="sxs-lookup"><span data-stu-id="7d74b-126">Add a generic claim check</span></span>

<span data-ttu-id="7d74b-127">Se o valor da declaração não é um único valor ou uma transformação é necessária, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="7d74b-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="7d74b-128">Para obter mais informações, consulte [usando um func para atender a uma política de](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="7d74b-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="7d74b-129">Avaliação de política múltipla</span><span class="sxs-lookup"><span data-stu-id="7d74b-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="7d74b-130">Se você aplicar várias políticas para um controlador ou ação, todas as políticas devem passar antes de conceder acesso.</span><span class="sxs-lookup"><span data-stu-id="7d74b-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="7d74b-131">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="7d74b-131">For example:</span></span>

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

<span data-ttu-id="7d74b-132">No exemplo acima qualquer identidade que atende a `EmployeeOnly` diretiva pode acessar o `Payslip` ação como essa política é imposta no controlador.</span><span class="sxs-lookup"><span data-stu-id="7d74b-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="7d74b-133">No entanto para chamar o `UpdateSalary` ação de identidade deve ser atendidos *ambos* o `EmployeeOnly` política e o `HumanResources` política.</span><span class="sxs-lookup"><span data-stu-id="7d74b-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="7d74b-134">Se você quiser políticas mais complicadas, como assumir uma data de nascimento de declaração, calculando uma idade dele, em seguida, verificando a idade é 21 ou mais antigo e em seguida, você precisa escrever [manipuladores de política personalizada](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="7d74b-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
