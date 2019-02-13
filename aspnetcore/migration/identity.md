---
title: Migrar autenticação e identidade para o ASP.NET Core
author: ardalis
description: Saiba como migrar autenticação e identidade de um projeto ASP.NET MVC para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: 72e62e78e37325ec47d54abbc11a875ae87fb63a
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "41824684"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Migrar autenticação e identidade para o ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

No artigo anterior, podemos [migrados de configuração de um projeto ASP.NET MVC para ASP.NET Core MVC](xref:migration/configuration). Neste artigo, vamos migrar os recursos de gerenciamento de registro, logon e usuário.

## <a name="configure-identity-and-membership"></a>Configurar identidade e associação

No ASP.NET MVC, os recursos de autenticação e identidade são configurados usando o ASP.NET Identity no *Startup.Auth.cs* e *IdentityConfig.cs*, localizado na *App_Start* pasta. No ASP.NET Core MVC, esses recursos são configurados na *Startup.cs*.

Instalar o `Microsoft.AspNetCore.Identity.EntityFrameworkCore` e `Microsoft.AspNetCore.Authentication.Cookies` pacotes do NuGet.

Em seguida, abra *Startup.cs* e atualize o `Startup.ConfigureServices` método para usar os serviços de identidade e o Entity Framework:

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

Neste ponto, há dois tipos referenciados no código acima que nós ainda não tiver migrado do projeto ASP.NET MVC: `ApplicationDbContext` e `ApplicationUser`. Criar um novo *modelos* pasta no ASP.NET Core de projeto e adicionar duas classes a ele correspondente a esses tipos. Você encontrará o ASP.NET MVC versões dessas classes em */Models/IdentityModels.cs*, mas usaremos um arquivo por classe no projeto migrado, já que é mais clara.

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFramework;
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

O projeto Web do ASP.NET Core MVC Starter não inclui muita personalização de usuários, ou o `ApplicationDbContext`. Ao migrar um aplicativo real, você também precisará migrar todas as propriedades personalizadas e métodos de usuário do seu aplicativo e `DbContext` classes, bem como outras classes de modelo utiliza o seu aplicativo. Por exemplo, se sua `DbContext` tem uma `DbSet<Album>`, você precisa para migrar o `Album` classe.

Com esses arquivos em vigor, o *Startup.cs* arquivo pode ser feito para compilar atualizando seu `using` instruções:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Nosso aplicativo agora está pronto para dar suporte a serviços de autenticação e identidade. Ele precisa apenas ter esses recursos expostos aos usuários.

## <a name="migrate-registration-and-login-logic"></a>Migrar a lógica de registro e login

Com os serviços de identidade configurados para o aplicativo e o acesso a dados configurado usando o Entity Framework e o SQL Server, estamos prontos para adicionar suporte para registro e logon para o aplicativo. Lembre-se de que [anterior no processo de migração](xref:migration/mvc#migrate-the-layout-file) estamos comentado uma referência a *loginpartial* na *layout. cshtml*. Agora é hora de retornar a esse código, remova os comentários e adicione os controladores de necessárias e os modos de exibição para dar suporte à funcionalidade de logon.

Remova os `@Html.Partial` de linha no *layout. cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Agora, adicione uma nova exibição do Razor chamada *loginpartial* para o *Views/Shared* pasta:

Atualização *loginpartial* com o código a seguir (substitua todo seu conteúdo):

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

Neste ponto, você deve ser capaz de atualizar o site no seu navegador.

## <a name="summary"></a>Resumo

ASP.NET Core apresenta alterações aos recursos de identidade do ASP.NET. Neste artigo, você viu como migrar os recursos de gerenciamento de usuário e autenticação de identidade do ASP.NET para ASP.NET Core.
