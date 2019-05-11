---
title: Personalização de modelo de identidade no ASP.NET Core
author: ajcvickers
description: Este artigo descreve como personalizar o modelo de dados subjacente do Entity Framework Core para ASP.NET Core Identity.
ms.author: avickers
ms.date: 04/24/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 53ce77e20722f3ba3282ff4455a0b70d30e635b0
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65536017"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Personalização de modelo de identidade no ASP.NET Core

Por [Arthur Vickers](https://github.com/ajcvickers)

Identidade do ASP.NET Core fornece uma estrutura para gerenciar e armazenar as contas de usuário em aplicativos ASP.NET Core. Identidade é adicionada ao seu projeto quando **contas de usuário individuais** é selecionado como o mecanismo de autenticação. Por padrão, a identidade faz usar de um Entity Framework (EF) modelo de dados central. Este artigo descreve como personalizar o modelo de identidade.

## <a name="identity-and-ef-core-migrations"></a>Identidade e migrações do EF Core

Antes de examinar o modelo, ele é útil para entender como a identidade funciona com [migrações do EF Core](/ef/core/managing-schemas/migrations/) para criar e atualizar um banco de dados. No nível superior, o processo é:

1. Definir ou atualizar uma [modelo de dados no código](/ef/core/modeling/).
1. Adicione uma migração para traduzir esse modelo para as alterações que podem ser aplicadas ao banco de dados.
1. Verifique que a migração representa corretamente suas intenções.
1. Aplique a migração para atualizar o banco de dados para ser sincronizado com o modelo.
1. Repita as etapas 1 a 4 para refinar o modelo ainda mais e manter o banco de dados em sincronia.

Use uma das abordagens a seguir para adicionar e aplicar as migrações:

* O **Package Manager Console** janela (PMC) se usando o Visual Studio. Para obter mais informações, consulte [ferramentas do EF Core PMC](/ef/core/miscellaneous/cli/powershell).
* A CLI do .NET Core se usando a linha de comando. Para obter mais informações, consulte [ferramentas de linha de comando do EF Core .NET](/ef/core/miscellaneous/cli/dotnet).
* Clicar a **aplicar migrações** botão na página de erro quando o aplicativo é executado.

O ASP.NET Core tem um manipulador de página de erro de tempo de desenvolvimento. O manipulador pode aplicar migrações quando o aplicativo é executado. Aplicativos de produção normalmente geram scripts SQL das migrações e implantar as alterações do banco de dados como parte de um aplicativo controlado e a implantação de banco de dados.

Quando um novo aplicativo usando a identidade é criado, as etapas 1 e 2 acima já tem sido concluídas. Ou seja, o modelo de dados iniciais já existir e a migração inicial foi adicionada ao projeto. A migração inicial ainda precisa ser aplicada ao banco de dados. A migração inicial pode ser aplicada por meio de uma das seguintes abordagens:

* Execute `Update-Database` no PMC.
* Executar `dotnet ef database update` em um shell de comando.
* Clique o **aplicar migrações** botão na página de erro quando o aplicativo é executado.

Repita as etapas anteriores, como as alterações são feitas no modelo.

## <a name="the-identity-model"></a>O modelo de identidade

### <a name="entity-types"></a>Tipos de entidade

O modelo de identidade consiste dos seguintes tipos de entidade.

|Tipo de entidade|Descrição                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Representa o usuário.                                         |
|`Role`     |Representa uma função.                                           |
|`UserClaim`|Representa uma declaração que um usuário possui.                    |
|`UserToken`|Representa um token de autenticação para um usuário.               |
|`UserLogin`|Associa um usuário com um logon.                              |
|`RoleClaim`|Representa uma declaração que é concedida a todos os usuários dentro de uma função.|
|`UserRole` |Uma entidade de junção que associa os usuários e funções.               |

### <a name="entity-type-relationships"></a>Relacionamentos de tipo de entidade

O [tipos de entidade](#entity-types) estão relacionados uns aos outros das seguintes maneiras:

* Cada `User` pode ter muitas `UserClaims`.
* Cada `User` pode ter muitas `UserLogins`.
* Cada `User` pode ter muitas `UserTokens`.
* Cada `Role` pode ter muitas associado `RoleClaims`.
* Cada `User` pode ter muitas associados `Roles`e cada `Role` pode ser associado a muitos `Users`. Essa é uma relação de muitos-para-muitos que requer uma tabela de junção no banco de dados. A tabela de junção é representada pelo `UserRole` entidade.

### <a name="default-model-configuration"></a>Configuração de modelo padrão

Identidade define muitos *classes de contexto* que herdam de <xref:Microsoft.EntityFrameworkCore.DbContext> para configurar e usar o modelo. Essa configuração é feita usando o [EF Core Fluent API do Code First](/ef/core/modeling/) no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*> método da classe de contexto. A configuração padrão é:

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

Identidade define o padrão [Common Language Runtime](/dotnet/standard/glossary#clr) tipos (CLR) para cada um dos tipos de entidade listados acima. Esses tipos são prefixados com *identidade*:

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Em vez de usar esses tipos diretamente, os tipos podem ser usados como classes base para os tipos do aplicativo. O `DbContext` classes definidas pela identidade são genéricas, de modo que diferentes tipos CLR podem ser usados para uma ou mais dos tipos de entidade no modelo. Esses tipos genéricos também permitem que o `User` tipo de dados (PK) chave primária a ser alterado.

Ao usar a identidade com suporte para funções, um <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> classe deve ser usada. Por exemplo:

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

Também é possível usar a identidade sem funções (apenas declarações), caso em que um <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> classe deve ser usada:

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

O ponto de partida para a personalização de modelo é derivado do tipo de contexto apropriado. Consulte a [modelar tipos genéricos](#model-generic-types) seção. Esse tipo de contexto geralmente é chamado `ApplicationDbContext` e é criada pelos modelos do ASP.NET Core.

O contexto é usado para configurar o modelo de duas maneiras:

* Fornecimento de entidade e tipos de chaves para os parâmetros de tipo genérico.
* Substituindo `OnModelCreating` para modificar o mapeamento desses tipos.

Ao substituir `OnModelCreating`, `base.OnModelCreating` deve ser chamado pela primeira vez; a configuração de substituição deve ser chamada em seguida. O EF Core geralmente tem uma política last-one-wins para a configuração. Por exemplo, se o `ToTable` método para um tipo de entidade é chamado pela primeira vez com o nome de uma tabela e, em seguida, novamente mais tarde com um nome de tabela diferente, o nome da tabela na segunda chamada é usado.

### <a name="custom-user-data"></a>Dados de usuário personalizada

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[Dados de usuário personalizados](xref:security/authentication/add-user-data) há suporte para herdar de `IdentityUser`. É comum para esse tipo de nome `ApplicationUser`:

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Use o `ApplicationUser` tipo como um argumento genérico para o contexto:

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

Não é necessário substituir `OnModelCreating` no `ApplicationDbContext` classe. O EF Core mapeia o `CustomTag` propriedade por convenção. No entanto, o banco de dados precisa ser atualizado para criar um novo `CustomTag` coluna. Para criar a coluna, adicione uma migração e, em seguida, atualize o banco de dados, conforme descrito em [identidade e migrações do EF Core](#identity-and-ef-core-migrations).

Atualização *Pages/Shared/_LoginPartial.cshtml* e substitua `IdentityUser` com `ApplicationUser`:

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Atualização *Areas/Identity/IdentityHostingStartup.cs* ou `Startup.ConfigureServices` e substituir `IdentityUser` com `ApplicationUser`.

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

No ASP.NET Core 2.1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor. Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>. Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Se o scaffolder de identidade foi usado para adicionar arquivos de identidade para o projeto, remova a chamada para `AddDefaultUI`. Para obter mais informações, consulte:

* [Identidade Scaffold](xref:security/authentication/scaffold-identity)
* [Adicionar, baixar e excluir dados de usuário personalizada à identidade](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Alterar o tipo de chave primária

Uma alteração para o tipo de dados da coluna PK depois que o banco de dados foi criado é um problema em muitos sistemas de banco de dados. Alterando a PK normalmente envolve descartar e recriar a tabela. Portanto, tipos de chave devem ser especificados na migração inicial quando o banco de dados é criado.

Siga estas etapas para alterar o tipo de PK:

1. Se o banco de dados foi criado antes da alteração de PK, execute `Drop-Database` (PMC) ou `dotnet ef database drop` (CLI do .NET Core) para excluí-lo.
2. Depois de confirmar a exclusão do banco de dados, remova a migração inicial com `Remove-Migration` (PMC) ou `dotnet ef migrations remove` (CLI do .NET Core).
3. Atualizar o `ApplicationDbContext` classe para derivar de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>. Especifique o novo tipo de chave para `TKey`. Por exemplo, para usar um `Guid` tipo de chave:

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

    No código anterior, as classes genéricas <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> deve ser especificado para usar o novo tipo de chave.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    No código anterior, as classes genéricas <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> deve ser especificado para usar o novo tipo de chave.

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

4. Se um personalizado `ApplicationUser` classe está sendo usado, atualize a classe para herdar de `IdentityUser`. Por exemplo:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Atualização `ApplicationDbContext` para fazer referência a personalizada `ApplicationUser` classe:

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

    Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.

    No ASP.NET Core 2.1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor. Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>. Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Se o scaffolder de identidade foi usado para adicionar arquivos de identidade para o projeto, remova a chamada para `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    O <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> método aceita um `TKey` tipo que indica o tipo de dados da chave primária.

    ::: moniker-end

5. Se um personalizado `ApplicationRole` classe está sendo usado, atualize a classe para herdar de `IdentityRole<TKey>`. Por exemplo:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Atualização `ApplicationDbContext` para fazer referência a personalizada `ApplicationRole` classe. Por exemplo, a classe a seguir faz referência a um personalizado `ApplicationUser` e um personalizado `ApplicationRole`:

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.

    No ASP.NET Core 2.1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor. Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>. Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Se o scaffolder de identidade foi usado para adicionar arquivos de identidade para o projeto, remova a chamada para `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    O <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> método aceita um `TKey` tipo que indica o tipo de dados da chave primária.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Adicionar propriedades de navegação

Alterar a configuração de modelo para relações pode ser mais difícil do que fazendo outras alterações. Deve-se ter cuidado para substituir as relações existentes em vez de criar novas relações adicionais. Em particular, a relação alterada deve especificar o mesmo (FK) propriedade de chave estrangeira como a relação existente. Por exemplo, a relação entre `Users` e `UserClaims` é, por padrão, especificada da seguinte maneira:

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

A FK para essa relação é especificada como o `UserClaim.UserId` propriedade. `HasMany` e `WithOne` são chamados sem argumentos para criar a relação sem propriedades de navegação.

Adicionar uma propriedade de navegação `ApplicationUser` que permite que associado `UserClaims` seja referenciado do usuário:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

O `TKey` para `IdentityUserClaim<TKey>` é o tipo especificado para o PK de usuários. Nesse caso, `TKey` é `string` porque os padrões estão sendo usados. Ele tem **não** o tipo de PK para o `UserClaim` tipo de entidade.

Agora que existe a propriedade de navegação, ele deve ser configurado no `OnModelCreating`:

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

Observe que a relação é configurada exatamente como ele era antes, apenas com uma propriedade de navegação especificada na chamada para `HasMany`.

As propriedades de navegação só existem no modelo do EF, não o banco de dados. Como a FK para a relação não foi alterado, esse tipo de alteração de modelo não exige o banco de dados a ser atualizada. Isso pode ser verificado com a adição de uma migração após fazer a alteração. O `Up` e `Down` métodos estão vazios.

### <a name="add-all-user-navigation-properties"></a>Adicionar todas as propriedades de navegação do usuário

O exemplo a seguir usando a seção acima como orientação, configura as propriedades de navegação unidirecional para todas as relações em usuário:

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

### <a name="add-user-and-role-navigation-properties"></a>Adicionar propriedades de navegação do usuário e a função

Usando a seção acima como orientação, o exemplo a seguir configura as propriedades de navegação para todas as relações no usuário e a função:

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

Notas:

* Este exemplo também inclui o `UserRole` entidade, que é necessário para navegar pela relação muitos-para-muitos dos usuários às funções de ingresso.
* Lembre-se de alterar os tipos das propriedades de navegação de refletir isso `ApplicationXxx` tipos agora estão sendo usados em vez de `IdentityXxx` tipos.
* Lembre-se de usar o `ApplicationXxx` no genérico `ApplicationContext` definição.

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

As seções anteriores demonstrou como alterar o tipo da chave usada no modelo de identidade. Alterando o modelo de chave de identidade para usar chaves compostas não é suportado nem recomendado. Usar uma chave composta com identidade envolve a alteração como o código do Gerenciador de identidade interage com o modelo. Essa personalização está além do escopo deste documento.

### <a name="change-tablecolumn-names-and-facets"></a>Alterar nomes de tabela/coluna e as facetas

Para alterar os nomes das tabelas e colunas, chame `base.OnModelCreating`. Em seguida, adicione a configuração para substituir qualquer um dos padrões. Por exemplo, para alterar o nome de todas as tabelas de identidade:

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

Esses exemplos usam os tipos de identidade padrão. Se usar um tipo de aplicativo, como `ApplicationUser`, configurar esse tipo em vez do tipo padrão.

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

Alguns tipos de colunas de banco de dados podem ser configurados com determinados *facetas* (por exemplo, o máximo `string` comprimento permitido). O exemplo a seguir define os comprimentos máximos da coluna para várias `string` propriedades no modelo:

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

Esquemas podem se comportar de maneira diferente em provedores de banco de dados. Para o SQL Server, o padrão é criar todas as tabelas na *dbo* esquema. As tabelas podem ser criadas em um esquema diferente. Por exemplo:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Carregamento lento

Nesta seção, o suporte para proxies de carregamento lento no modelo de identidade é adicionado. Carregamento lento é útil, pois ele permite que as propriedades de navegação a ser usada sem primeiro garantir que eles são carregados.

Tipos de entidade podem ser feitos adequados para carregamento lento de diversas maneiras, conforme descrito na [documentação do EF Core](/ef/core/querying/related-data#lazy-loading). Para simplificar, use proxies de carregamento lento, que requer:

* Instalação dos [entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) pacote.
* Uma chamada para <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> dentro de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>.
* Tipos de entidade pública com `public virtual` propriedades de navegação.

O exemplo a seguir demonstra a chamada `UseLazyLoadingProxies` em `Startup.ConfigureServices`:

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Consulte os exemplos anteriores para obter orientação sobre como adicionar propriedades de navegação para os tipos de entidade.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authentication/scaffold-identity>

::: moniker-end
