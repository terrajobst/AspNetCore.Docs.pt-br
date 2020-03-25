---
title: Migrar autenticação e identidade para ASP.NET Core
author: ardalis
description: Saiba como migrar a autenticação e a identidade de um projeto MVC do ASP.NET para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 3/22/2020
uid: migration/identity
ms.openlocfilehash: c5727c974e455144d04e66fe14ea591e160cb963
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219188"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Migrar autenticação e identidade para ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

No artigo anterior, [migramos a configuração de um projeto MVC ASP.net para ASP.NET Core MVC](xref:migration/configuration). Neste artigo, migramos os recursos de registro, logon e gerenciamento de usuário.

## <a name="configure-identity-and-membership"></a>Configurar identidade e Associação

No ASP.NET MVC, os recursos de autenticação e identidade são configurados usando ASP.NET Identity em *Startup.auth.cs* e *IdentityConfig.cs*, localizados na pasta *App_Start* . No ASP.NET Core MVC, esses recursos são configurados em *Startup.cs*.

Instale os seguintes pacotes NuGet:

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

No *Startup.cs*, atualize o método `Startup.ConfigureServices` para usar os serviços de Entity Framework e identidade:

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

Neste ponto, há dois tipos referenciados no código acima que ainda não migramos do projeto MVC ASP.NET: `ApplicationDbContext` e `ApplicationUser`. Crie uma nova pasta *modelos* no projeto ASP.NET Core e adicione duas classes a ela correspondente a esses tipos. Você encontrará as versões do ASP.NET MVC dessas classes no */Models/IdentityModels.cs*, mas usaremos um arquivo por classe no projeto migrado, pois isso é mais claro.

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

O ASP.NET Core projeto da Web inicial do MVC não inclui muita personalização de usuários ou o `ApplicationDbContext`. Ao migrar um aplicativo real, você também precisa migrar todas as propriedades e métodos personalizados do usuário e das classes de `DbContext` do seu aplicativo, bem como quaisquer outras classes de modelo que seu aplicativo utiliza. Por exemplo, se o `DbContext` tiver uma `DbSet<Album>`, você precisará migrar a classe `Album`.

Com esses arquivos em vigor, o arquivo *Startup.cs* pode ser feito para compilar atualizando suas instruções de `using`:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Nosso aplicativo agora está pronto para dar suporte a serviços de autenticação e identidade. Ele só precisa ter esses recursos expostos aos usuários.

## <a name="migrate-registration-and-login-logic"></a>Migrar o registro e a lógica de logon

Com os serviços de identidade configurados para o aplicativo e acesso a dados configurados usando Entity Framework e SQL Server, estamos prontos para adicionar suporte para registro e logon no aplicativo. Lembre-se de que, [anteriormente no processo de migração](xref:migration/mvc#migrate-the-layout-file) , comentamos uma referência a *_LoginPartial* em *_Layout. cshtml*. Agora é hora de retornar a esse código, remover os comentários e adicionar os controladores e exibições necessários para dar suporte à funcionalidade de logon.

Remova a marca de comentário da linha de `@Html.Partial` em *_Layout. cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Agora, adicione uma nova exibição do Razor chamada *_LoginPartial* à pasta *views/Shared* :

Atualize *_LoginPartial. cshtml* com o seguinte código (substitua todo o seu conteúdo):

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

ASP.NET Core introduz alterações nos recursos de ASP.NET Identity. Neste artigo, você viu como migrar os recursos de autenticação e gerenciamento de usuários do ASP.NET Identity para o ASP.NET Core.
