---
title: Personalização do modelo de identidade no ASP.NET Core
author: ajcvickers
description: Este artigo descreve como personalizar o modelo de dados de Entity Framework Core subjacente para ASP.NET Core identidade.
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656075"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Personalização do modelo de identidade no ASP.NET Core

Por [Arthur Vicker](https://github.com/ajcvickers)

ASP.NET Core identidade fornece uma estrutura para gerenciar e armazenar contas de usuário em aplicativos ASP.NET Core. A identidade é adicionada ao seu projeto quando **contas de usuário individuais** são selecionadas como o mecanismo de autenticação. Por padrão, a identidade usa um modelo de dados principal do Entity Framework (EF). Este artigo descreve como personalizar o modelo de identidade.

## <a name="identity-and-ef-core-migrations"></a>Migrações de EF Core e identidade

Antes de examinar o modelo, é útil entender como a identidade funciona com [EF Core migrações](/ef/core/managing-schemas/migrations/) para criar e atualizar um banco de dados. No nível superior, o processo é:

1. Definir ou atualizar um [modelo de dados no código](/ef/core/modeling/).
1. Adicione uma migração para converter esse modelo em alterações que podem ser aplicadas ao banco de dados.
1. Verifique se a migração representa corretamente suas intenções.
1. Aplique a migração para atualizar o banco de dados a ser sincronizado com o modelo.
1. Repita as etapas de 1 a 4 para refinar ainda mais o modelo e manter o banco de dados em sincronia.

Use uma das seguintes abordagens para adicionar e aplicar migrações:

* A janela do **console do Gerenciador de pacotes** (PMC) se estiver usando o Visual Studio. Para obter mais informações, consulte [Ferramentas do EF Core PMC](/ef/core/miscellaneous/cli/powershell).
* O CLI do .NET Core se estiver usando a linha de comando. Para obter mais informações, consulte [EF Core ferramentas de linha de comando do .net](/ef/core/miscellaneous/cli/dotnet).
* Clicando no botão **aplicar migrações** na página de erro quando o aplicativo é executado.

ASP.NET Core tem um manipulador de página de erro de tempo de desenvolvimento. O manipulador pode aplicar migrações quando o aplicativo é executado. Os aplicativos de produção normalmente geram scripts SQL das migrações e implantam alterações no banco de dados como parte de uma implantação de banco de dados e aplicativo controlado.

Quando um novo aplicativo usando a identidade é criado, as etapas 1 e 2 acima já foram concluídas. Ou seja, o modelo de dados inicial já existe e a migração inicial foi adicionada ao projeto. A migração inicial ainda precisa ser aplicada ao banco de dados. A migração inicial pode ser aplicada por meio de uma das seguintes abordagens:

* Execute `Update-Database` no PMC.
* Execute `dotnet ef database update` em um shell de comando.
* Clique no botão **aplicar migrações** na página de erro quando o aplicativo for executado.

Repita as etapas anteriores conforme as alterações são feitas no modelo.

## <a name="the-identity-model"></a>O modelo de identidade

### <a name="entity-types"></a>Tipos de entidade

O modelo de identidade consiste nos seguintes tipos de entidade.

|Tipo de entidade|Descrição                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Representa o usuário.                                         |
|`Role`     |Representa uma função.                                           |
|`UserClaim`|Representa uma declaração que um usuário possui.                    |
|`UserToken`|Representa um token de autenticação para um usuário.               |
|`UserLogin`|Associa um usuário a um logon.                              |
|`RoleClaim`|Representa uma declaração que é concedida a todos os usuários dentro de uma função.|
|`UserRole` |Uma entidade de junção que associa usuários e funções.               |

### <a name="entity-type-relationships"></a>Relações de tipo de entidade

Os [tipos de entidade](#entity-types) estão relacionados entre si das seguintes maneiras:

* Cada `User` pode ter muitos `UserClaims`.
* Cada `User` pode ter muitos `UserLogins`.
* Cada `User` pode ter muitos `UserTokens`.
* Cada `Role` pode ter muitos `RoleClaims`associados.
* Cada `User` pode ter muitos `Roles`associados, e cada `Role` pode ser associado a muitos `Users`. Essa é uma relação muitos-para-muitos que requer uma tabela de junção no banco de dados. A tabela de junção é representada pela entidade `UserRole`.

### <a name="default-model-configuration"></a>Configuração de modelo padrão

A identidade define muitas *classes de contexto* que herdam de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) para configurar e usar o modelo. Essa configuração é feita usando o [EF Core Code First API fluente](/ef/core/modeling/) no método [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) da classe de contexto. A configuração padrão é:

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a>Tipos genéricos de modelo

A identidade define os tipos padrão CLR ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) para cada um dos tipos de entidade listados acima. Esses tipos são todos prefixados com *identidade*:

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Em vez de usar esses tipos diretamente, os tipos podem ser usados como classes base para os próprios tipos do aplicativo. As classes de `DbContext` definidas por Identity são genéricas, de modo que diferentes tipos CLR possam ser usados para um ou mais tipos de entidade no modelo. Esses tipos genéricos também permitem que o tipo de dados de `User` chave primária (CP) seja alterado.

Ao usar a identidade com suporte para funções, uma classe de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> deve ser usada. Por exemplo:

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

Também é possível usar a identidade sem funções (apenas declarações), caso em que uma classe de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> deve ser usada:

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>Personalizar o modelo

O ponto de partida para a personalização do modelo é derivar do tipo de contexto apropriado. Consulte a seção [tipos genéricos de modelo](#model-generic-types) . Esse tipo de contexto é normalmente chamado `ApplicationDbContext` e é criado pelos modelos de ASP.NET Core.

O contexto é usado para configurar o modelo de duas maneiras:

* Fornecendo tipos de entidade e de chave para os parâmetros de tipo genérico.
* Substituir `OnModelCreating` para modificar o mapeamento desses tipos.

Ao substituir `OnModelCreating`, `base.OnModelCreating` deve ser chamado primeiro; a configuração de substituição deve ser chamada em seguida. EF Core geralmente tem uma política do último-um-WINS para configuração. Por exemplo, se o método `ToTable` para um tipo de entidade for chamado primeiro com um nome de tabela e, em seguida, novamente mais tarde com um nome de tabela diferente, o nome da tabela na segunda chamada será usado.

### <a name="custom-user-data"></a>Dados de usuário personalizados

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[Os dados de usuário personalizados](xref:security/authentication/add-user-data) têm suporte ao herdar de `IdentityUser`. É personalizado nomear este tipo `ApplicationUser`:

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Use o tipo de `ApplicationUser` como um argumento genérico para o contexto:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

Não é necessário substituir `OnModelCreating` na classe `ApplicationDbContext`. EF Core mapeia a propriedade `CustomTag` por convenção. No entanto, o banco de dados precisa ser atualizado para criar uma nova coluna `CustomTag`. Para criar a coluna, adicione uma migração e, em seguida, atualize o banco de dados conforme descrito em [EF Core migrações de identidade e](#identity-and-ef-core-migrations)de.

Atualize as *páginas/Shared/_LoginPartial. cshtml* e substitua `IdentityUser` por `ApplicationUser`:

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Atualize *áreas/identidade/IdentityHostingStartup. cs* ou `Startup.ConfigureServices` e substitua `IdentityUser` por `ApplicationUser`.

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

No ASP.NET Core 2,1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor. Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>. Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Se o scaffolder de identidade tiver sido usado para adicionar arquivos de identidade ao projeto, remova a chamada para `AddDefaultUI`. Para obter mais informações, consulte:

* [Identidade Scaffold](xref:security/authentication/scaffold-identity)
* [Adicionar, baixar e excluir dados de usuário personalizados para identidade](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Alterar o tipo de chave primária

Uma alteração no tipo de dados da coluna CP depois que o banco de dado foi criado é problemática em muitos sistemas de banco de dados. A alteração da CP normalmente envolve a remoção e a recriação da tabela. Portanto, os tipos de chave devem ser especificados na migração inicial quando o banco de dados é criado.

Siga estas etapas para alterar o tipo de CP:

1. Se o banco de dados foi criado antes da alteração de CP, execute `Drop-Database` (PMC) ou `dotnet ef database drop` (CLI do .NET Core) para excluí-lo.
2. Depois de confirmar a exclusão do banco de dados, remova a migração inicial com `Remove-Migration` (PMC) ou `dotnet ef migrations remove` (CLI do .NET Core).
3. Atualize a classe `ApplicationDbContext` para derivar de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>. Especifique o novo tipo de chave para `TKey`. Por exemplo, para usar um tipo de chave `Guid`:

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    No código anterior, as classes genéricas <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> devem ser especificadas para usar o novo tipo de chave.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    No código anterior, as classes genéricas <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> devem ser especificadas para usar o novo tipo de chave.

    ::: moniker-end

    `Startup.ConfigureServices` deve ser atualizado para usar o usuário genérico:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. Se uma classe de `ApplicationUser` personalizada estiver sendo usada, atualize a classe para herdar de `IdentityUser`. Por exemplo:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Atualize `ApplicationDbContext` para referenciar a classe de `ApplicationUser` personalizada:

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    Registre a classe de contexto do banco de dados personalizado ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    O tipo de dados da chave primária é inferido pela análise do objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    No ASP.NET Core 2,1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor. Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>. Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Se o scaffolder de identidade tiver sido usado para adicionar arquivos de identidade ao projeto, remova a chamada para `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    O tipo de dados da chave primária é inferido pela análise do objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    O método <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> aceita um tipo de `TKey` indicando o tipo de dados da chave primária.

    ::: moniker-end

5. Se uma classe de `ApplicationRole` personalizada estiver sendo usada, atualize a classe para herdar de `IdentityRole<TKey>`. Por exemplo:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Atualize `ApplicationDbContext` para referenciar a classe de `ApplicationRole` personalizada. Por exemplo, a classe a seguir faz referência a uma `ApplicationUser` personalizada e a uma `ApplicationRole`personalizada:

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registre a classe de contexto do banco de dados personalizado ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    O tipo de dados da chave primária é inferido pela análise do objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    No ASP.NET Core 2,1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor. Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>. Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Se o scaffolder de identidade tiver sido usado para adicionar arquivos de identidade ao projeto, remova a chamada para `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registre a classe de contexto do banco de dados personalizado ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    O tipo de dados da chave primária é inferido pela análise do objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registre a classe de contexto do banco de dados personalizado ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    O método <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> aceita um tipo de `TKey` indicando o tipo de dados da chave primária.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Adicionar propriedades de navegação

Alterar a configuração do modelo para relações pode ser mais difícil do que fazer outras alterações. Deve-se ter cuidado para substituir as relações existentes em vez de criar relações novas e adicionais. Em particular, a relação alterada deve especificar a mesma propriedade de chave estrangeira (FK) que a relação existente. Por exemplo, a relação entre `Users` e `UserClaims` é, por padrão, especificada da seguinte maneira:

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

O FK para essa relação é especificado como a propriedade `UserClaim.UserId`. `HasMany` e `WithOne` são chamados sem argumentos para criar a relação sem propriedades de navegação.

Adicione uma propriedade de navegação a `ApplicationUser` que permite que os `UserClaims` associados sejam referenciados do usuário:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

O `TKey` para `IdentityUserClaim<TKey>` é o tipo especificado para a CP de usuários. Nesse caso, `TKey` é `string` porque os padrões estão sendo usados. **Não** é o tipo de CP para o tipo de entidade `UserClaim`.

Agora que a propriedade de navegação existe, ela deve ser configurada no `OnModelCreating`:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

Observe que a relação está configurada exatamente como estava antes, somente com uma propriedade de navegação especificada na chamada para `HasMany`.

As propriedades de navegação existem somente no modelo EF, não no banco de dados. Como o FK da relação não foi alterado, esse tipo de alteração de modelo não exige que o banco de dados seja atualizado. Isso pode ser verificado adicionando uma migração depois de fazer a alteração. Os métodos `Up` e `Down` estão vazios.

### <a name="add-all-user-navigation-properties"></a>Adicionar todas as propriedades de navegação do usuário

Usando a seção acima como orientação, o exemplo a seguir configura as propriedades de navegação unidirecionais para todas as relações no usuário:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a>Adicionar propriedades de navegação de usuário e função

Usando a seção acima como orientação, o exemplo a seguir configura as propriedades de navegação para todas as relações no usuário e na função:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

Observações:

* Este exemplo também inclui a entidade `UserRole` Join, que é necessária para navegar na relação muitos para muitos de usuários para funções.
* Lembre-se de alterar os tipos das propriedades de navegação para refletir que os tipos de `ApplicationXxx` agora estão sendo usados em vez de `IdentityXxx` tipos.
* Lembre-se de usar o `ApplicationXxx` na definição `ApplicationContext` genérica.

### <a name="add-all-navigation-properties"></a>Adicionar todas as propriedades de navegação

Usando a seção acima como orientação, o exemplo a seguir configura as propriedades de navegação para todas as relações em todos os tipos de entidade:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a>Usar chaves compostas

As seções anteriores demonstraram a alteração do tipo de chave usada no modelo de identidade. Não há suporte ou recomendado para alterar o modelo de chave de identidade para usar chaves compostas. O uso de uma chave composta com identidade envolve a alteração de como o código do Identity Manager interage com o modelo. Essa personalização está além do escopo deste documento.

### <a name="change-tablecolumn-names-and-facets"></a>Alterar nomes e facetas de tabela/coluna

Para alterar os nomes de tabelas e colunas, chame `base.OnModelCreating`. Em seguida, adicione a configuração para substituir qualquer um dos padrões. Por exemplo, para alterar o nome de todas as tabelas de identidade:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

Esses exemplos usam os tipos de identidade padrão. Se estiver usando um tipo de aplicativo como `ApplicationUser`, configure esse tipo em vez do tipo padrão.

O exemplo a seguir altera alguns nomes de coluna:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

Alguns tipos de colunas de banco de dados podem ser configurados com determinadas *facetas* (por exemplo, o comprimento máximo de `string` permitido). O exemplo a seguir define comprimentos máximos de coluna para várias propriedades `string` no modelo:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>Mapear para um esquema diferente

Os esquemas podem se comportar de forma diferente nos provedores de banco de dados Por SQL Server, o padrão é criar todas as tabelas no esquema *dbo* . As tabelas podem ser criadas em um esquema diferente. Por exemplo:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Carregamento lento

Nesta seção, o suporte para proxies de carregamento lento no modelo de identidade é adicionado. O carregamento lento é útil, pois permite que as propriedades de navegação sejam usadas sem primeiro garantir que estejam carregadas.

Tipos de entidade podem ser adequados para carregamento lento de várias maneiras, conforme descrito na documentação do [EF Core](/ef/core/querying/related-data#lazy-loading). Para simplificar, use proxies de carregamento lento, o que exige:

* Instalação do pacote [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .
* Uma chamada para <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> dentro de [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Tipos de entidade pública com propriedades de navegação `public virtual`.

O exemplo a seguir demonstra como chamar `UseLazyLoadingProxies` no `Startup.ConfigureServices`:

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Consulte os exemplos anteriores para obter orientação sobre como adicionar propriedades de navegação aos tipos de entidade.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authentication/scaffold-identity>

::: moniker-end
