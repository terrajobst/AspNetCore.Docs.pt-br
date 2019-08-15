---
title: Migrar autenticação e identidade para ASP.NET Core
author: ardalis
description: Saiba como migrar a autenticação e a identidade de um projeto MVC do ASP.NET para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022277"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="8dce5-103">Migrar autenticação e identidade para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8dce5-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="8dce5-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8dce5-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8dce5-105">No artigo anterior, migramos [a configuração de um projeto MVC ASP.net para ASP.NET Core MVC](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="8dce5-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="8dce5-106">Neste artigo, migramos os recursos de registro, logon e gerenciamento de usuário.</span><span class="sxs-lookup"><span data-stu-id="8dce5-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="8dce5-107">Configurar identidade e Associação</span><span class="sxs-lookup"><span data-stu-id="8dce5-107">Configure Identity and Membership</span></span>

<span data-ttu-id="8dce5-108">No ASP.NET MVC, os recursos de autenticação e identidade são configurados usando ASP.NET Identity em *Startup.auth.cs* e *IdentityConfig.cs*, localizados na pasta *App_Start* .</span><span class="sxs-lookup"><span data-stu-id="8dce5-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="8dce5-109">No ASP.NET Core MVC, esses recursos são configurados em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="8dce5-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="8dce5-110">Instale os `Microsoft.AspNetCore.Identity.EntityFrameworkCore` pacotes `Microsoft.AspNetCore.Authentication.Cookies` do e do NuGet.</span><span class="sxs-lookup"><span data-stu-id="8dce5-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="8dce5-111">Em seguida, abra *Startup.cs* e atualize `Startup.ConfigureServices` o método para usar Entity Framework e serviços de identidade:</span><span class="sxs-lookup"><span data-stu-id="8dce5-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="8dce5-112">Neste ponto, há dois tipos referenciados no código acima que ainda não migramos do projeto MVC do ASP.net: `ApplicationDbContext` e. `ApplicationUser`</span><span class="sxs-lookup"><span data-stu-id="8dce5-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="8dce5-113">Crie uma nova pasta *modelos* no projeto ASP.NET Core e adicione duas classes a ela correspondente a esses tipos.</span><span class="sxs-lookup"><span data-stu-id="8dce5-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="8dce5-114">Você encontrará as versões do ASP.NET MVC dessas classes no */Models/IdentityModels.cs*, mas usaremos um arquivo por classe no projeto migrado, pois isso é mais claro.</span><span class="sxs-lookup"><span data-stu-id="8dce5-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="8dce5-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="8dce5-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="8dce5-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8dce5-116">*ApplicationDbContext.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="8dce5-117">O projeto Web do ASP.NET Core do MVC inicial não inclui muita personalização de usuários ou o `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="8dce5-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="8dce5-118">Ao migrar um aplicativo real, você também precisa migrar todas as propriedades e métodos personalizados do usuário e `DbContext` das classes do seu aplicativo, bem como quaisquer outras classes de modelo que seu aplicativo utiliza.</span><span class="sxs-lookup"><span data-stu-id="8dce5-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="8dce5-119">Por exemplo, se o `DbContext` tiver um `DbSet<Album>`, você precisará migrar `Album` a classe.</span><span class="sxs-lookup"><span data-stu-id="8dce5-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="8dce5-120">Com esses arquivos em vigor, o arquivo *Startup.cs* pode ser feito para compilar atualizando suas `using` instruções:</span><span class="sxs-lookup"><span data-stu-id="8dce5-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="8dce5-121">Nosso aplicativo agora está pronto para dar suporte a serviços de autenticação e identidade.</span><span class="sxs-lookup"><span data-stu-id="8dce5-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="8dce5-122">Ele só precisa ter esses recursos expostos aos usuários.</span><span class="sxs-lookup"><span data-stu-id="8dce5-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="8dce5-123">Migrar o registro e a lógica de logon</span><span class="sxs-lookup"><span data-stu-id="8dce5-123">Migrate registration and login logic</span></span>

<span data-ttu-id="8dce5-124">Com os serviços de identidade configurados para o aplicativo e acesso a dados configurados usando Entity Framework e SQL Server, estamos prontos para adicionar suporte para registro e logon no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8dce5-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="8dce5-125">Lembre-se de que, [anteriormente no processo de migração](xref:migration/mvc#migrate-the-layout-file) , comentamos uma referência a *_LoginPartial* em *_ layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8dce5-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="8dce5-126">Agora é hora de retornar a esse código, remover os comentários e adicionar os controladores e exibições necessários para dar suporte à funcionalidade de logon.</span><span class="sxs-lookup"><span data-stu-id="8dce5-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="8dce5-127">Remova a marca `@Html.Partial` de comentário da linha em *_ layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8dce5-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="8dce5-128">Agora, adicione uma nova exibição do Razor chamada *_LoginPartial* à pasta *views/Shared* :</span><span class="sxs-lookup"><span data-stu-id="8dce5-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="8dce5-129">Atualize *_LoginPartial. cshtml* com o seguinte código (substitua todo o seu conteúdo):</span><span class="sxs-lookup"><span data-stu-id="8dce5-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

<span data-ttu-id="8dce5-130">Neste ponto, você deve ser capaz de atualizar o site no seu navegador.</span><span class="sxs-lookup"><span data-stu-id="8dce5-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="8dce5-131">Resumo</span><span class="sxs-lookup"><span data-stu-id="8dce5-131">Summary</span></span>

<span data-ttu-id="8dce5-132">ASP.NET Core introduz alterações nos recursos de ASP.NET Identity.</span><span class="sxs-lookup"><span data-stu-id="8dce5-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="8dce5-133">Neste artigo, você viu como migrar os recursos de autenticação e gerenciamento de usuários do ASP.NET Identity para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8dce5-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
