---
title: Personalização de modelo de identidade no ASP.NET Core
author: ajcvickers
description: Este artigo descreve como personalizar o modelo de dados subjacente do Entity Framework Core para ASP.NET Core Identity.
ms.author: avickers
ms.date: 09/24/2018
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 55346c571f180fa17a1108a622d991d15f365bae
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209456"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="55315-103">Personalização de modelo de identidade no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55315-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="55315-104">Por [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="55315-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="55315-105">Identidade do ASP.NET Core fornece uma estrutura para gerenciar e armazenar as contas de usuário em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="55315-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="55315-106">Identidade é adicionada ao seu projeto quando **contas de usuário individuais** é selecionado como o mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="55315-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="55315-107">Por padrão, a identidade faz usar de um Entity Framework (EF) modelo de dados central.</span><span class="sxs-lookup"><span data-stu-id="55315-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="55315-108">Este artigo descreve como personalizar o modelo de identidade.</span><span class="sxs-lookup"><span data-stu-id="55315-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="55315-109">Identidade e migrações do EF Core</span><span class="sxs-lookup"><span data-stu-id="55315-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="55315-110">Antes de examinar o modelo, ele é útil para entender como a identidade funciona com [migrações do EF Core](/ef/core/managing-schemas/migrations/) para criar e atualizar um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="55315-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="55315-111">No nível superior, o processo é:</span><span class="sxs-lookup"><span data-stu-id="55315-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="55315-112">Definir ou atualizar uma [modelo de dados no código](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="55315-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="55315-113">Adicione uma migração para traduzir esse modelo para as alterações que podem ser aplicadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="55315-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="55315-114">Verifique que a migração representa corretamente suas intenções.</span><span class="sxs-lookup"><span data-stu-id="55315-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="55315-115">Aplique a migração para atualizar o banco de dados para ser sincronizado com o modelo.</span><span class="sxs-lookup"><span data-stu-id="55315-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="55315-116">Repita as etapas 1 a 4 para refinar o modelo ainda mais e manter o banco de dados em sincronia.</span><span class="sxs-lookup"><span data-stu-id="55315-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="55315-117">Use uma das abordagens a seguir para adicionar e aplicar as migrações:</span><span class="sxs-lookup"><span data-stu-id="55315-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="55315-118">O **Package Manager Console** janela (PMC) se usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="55315-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="55315-119">Para obter mais informações, consulte [ferramentas do EF Core PMC](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="55315-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="55315-120">A CLI do .NET Core se usando a linha de comando.</span><span class="sxs-lookup"><span data-stu-id="55315-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="55315-121">Para obter mais informações, consulte [ferramentas de linha de comando do EF Core .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="55315-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="55315-122">Clicar a **aplicar migrações** botão na página de erro quando o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="55315-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="55315-123">O ASP.NET Core tem um manipulador de página de erro de tempo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="55315-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="55315-124">O manipulador pode aplicar migrações quando o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="55315-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="55315-125">Para aplicativos de produção, geralmente é mais apropriado para gerar scripts SQL das migrações e implantar as alterações do banco de dados como parte de uma implantação de aplicativo e o banco de dados controlada.</span><span class="sxs-lookup"><span data-stu-id="55315-125">For production apps, it's often more appropriate to generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="55315-126">Quando um novo aplicativo usando a identidade é criado, as etapas 1 e 2 acima já tem sido concluídas.</span><span class="sxs-lookup"><span data-stu-id="55315-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="55315-127">Ou seja, o modelo de dados iniciais já existir e a migração inicial foi adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="55315-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="55315-128">A migração inicial ainda precisa ser aplicada ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="55315-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="55315-129">A migração inicial pode ser aplicada por meio de uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="55315-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="55315-130">Execute `Update-Database` no PMC.</span><span class="sxs-lookup"><span data-stu-id="55315-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="55315-131">Executar `dotnet ef database update` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="55315-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="55315-132">Clique o **aplicar migrações** botão na página de erro quando o aplicativo é executado.</span><span class="sxs-lookup"><span data-stu-id="55315-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="55315-133">Repita as etapas anteriores, como as alterações são feitas no modelo.</span><span class="sxs-lookup"><span data-stu-id="55315-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="55315-134">O modelo de identidade</span><span class="sxs-lookup"><span data-stu-id="55315-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="55315-135">Tipos de entidade</span><span class="sxs-lookup"><span data-stu-id="55315-135">Entity types</span></span>

<span data-ttu-id="55315-136">O modelo de identidade consiste dos seguintes tipos de entidade.</span><span class="sxs-lookup"><span data-stu-id="55315-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="55315-137">Tipo de entidade</span><span class="sxs-lookup"><span data-stu-id="55315-137">Entity type</span></span>|<span data-ttu-id="55315-138">Descrição</span><span class="sxs-lookup"><span data-stu-id="55315-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="55315-139">Representa o usuário.</span><span class="sxs-lookup"><span data-stu-id="55315-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="55315-140">Representa uma função.</span><span class="sxs-lookup"><span data-stu-id="55315-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="55315-141">Representa uma declaração que um usuário possui.</span><span class="sxs-lookup"><span data-stu-id="55315-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="55315-142">Representa um token de autenticação para um usuário.</span><span class="sxs-lookup"><span data-stu-id="55315-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="55315-143">Associa um usuário com um logon.</span><span class="sxs-lookup"><span data-stu-id="55315-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="55315-144">Representa uma declaração que é concedida a todos os usuários dentro de uma função.</span><span class="sxs-lookup"><span data-stu-id="55315-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="55315-145">Uma entidade de junção que associa os usuários e funções.</span><span class="sxs-lookup"><span data-stu-id="55315-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="55315-146">Relacionamentos de tipo de entidade</span><span class="sxs-lookup"><span data-stu-id="55315-146">Entity type relationships</span></span>

<span data-ttu-id="55315-147">O [tipos de entidade](#entity-types) estão relacionados uns aos outros das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="55315-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="55315-148">Cada `User` pode ter muitas `UserClaims`.</span><span class="sxs-lookup"><span data-stu-id="55315-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="55315-149">Cada `User` pode ter muitas `UserLogins`.</span><span class="sxs-lookup"><span data-stu-id="55315-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="55315-150">Cada `User` pode ter muitas `UserTokens`.</span><span class="sxs-lookup"><span data-stu-id="55315-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="55315-151">Cada `Role` pode ter muitas associado `RoleClaims`.</span><span class="sxs-lookup"><span data-stu-id="55315-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="55315-152">Cada `User` pode ter muitas associados `Roles`e cada `Role` pode ser associado a muitos `Users`.</span><span class="sxs-lookup"><span data-stu-id="55315-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="55315-153">Essa é uma relação de muitos-para-muitos que requer uma tabela de junção no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="55315-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="55315-154">A tabela de junção é representada pelo `UserRole` entidade.</span><span class="sxs-lookup"><span data-stu-id="55315-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="55315-155">Configuração de modelo padrão</span><span class="sxs-lookup"><span data-stu-id="55315-155">Default model configuration</span></span>

<span data-ttu-id="55315-156">Identidade define muitos *classes de contexto* que herdam de <xref:Microsoft.EntityFrameworkCore.DbContext> para configurar e usar o modelo.</span><span class="sxs-lookup"><span data-stu-id="55315-156">Identity defines many *context classes* that inherit from <xref:Microsoft.EntityFrameworkCore.DbContext> to configure and use the model.</span></span> <span data-ttu-id="55315-157">Essa configuração é feita usando o [EF Core Fluent API do Code First](/ef/core/modeling/) no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*> método da classe de contexto.</span><span class="sxs-lookup"><span data-stu-id="55315-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*> method of the context class.</span></span> <span data-ttu-id="55315-158">A configuração padrão é:</span><span class="sxs-lookup"><span data-stu-id="55315-158">The default configuration is:</span></span>

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

### <a name="model-generic-types"></a><span data-ttu-id="55315-159">Tipos genéricos de modelo</span><span class="sxs-lookup"><span data-stu-id="55315-159">Model generic types</span></span>

<span data-ttu-id="55315-160">Identidade define o padrão [Common Language Runtime](/dotnet/standard/glossary#clr) tipos (CLR) para cada um dos tipos de entidade listados acima.</span><span class="sxs-lookup"><span data-stu-id="55315-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="55315-161">Esses tipos são prefixados com *identidade*:</span><span class="sxs-lookup"><span data-stu-id="55315-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="55315-162">Em vez de usar esses tipos diretamente, os tipos podem ser usados como classes base para os tipos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="55315-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="55315-163">O `DbContext` classes definidas pela identidade são genéricas, de modo que diferentes tipos CLR podem ser usados para uma ou mais dos tipos de entidade no modelo.</span><span class="sxs-lookup"><span data-stu-id="55315-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="55315-164">Esses tipos genéricos também permitem que o `User` tipo de dados (PK) chave primária a ser alterado.</span><span class="sxs-lookup"><span data-stu-id="55315-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="55315-165">Ao usar a identidade com suporte para funções, um <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> classe deve ser usada.</span><span class="sxs-lookup"><span data-stu-id="55315-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="55315-166">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="55315-166">For example:</span></span>

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

<span data-ttu-id="55315-167">Também é possível usar a identidade sem funções (apenas declarações), caso em que um <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext`1> classe deve ser usada:</span><span class="sxs-lookup"><span data-stu-id="55315-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext`1> class should be used:</span></span>

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

## <a name="customize-the-model"></a><span data-ttu-id="55315-168">Personalizar o modelo</span><span class="sxs-lookup"><span data-stu-id="55315-168">Customize the model</span></span>

<span data-ttu-id="55315-169">O ponto de partida para a personalização de modelo é derivado do tipo de contexto apropriado.</span><span class="sxs-lookup"><span data-stu-id="55315-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="55315-170">Consulte a [modelar tipos genéricos](#model-generic-types) seção.</span><span class="sxs-lookup"><span data-stu-id="55315-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="55315-171">Esse tipo de contexto geralmente é chamado `ApplicationDbContext` e é criada pelos modelos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="55315-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="55315-172">O contexto é usado para configurar o modelo de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="55315-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="55315-173">Fornecimento de entidade e tipos de chaves para os parâmetros de tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="55315-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="55315-174">Substituindo `OnModelCreating` para modificar o mapeamento desses tipos.</span><span class="sxs-lookup"><span data-stu-id="55315-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="55315-175">Ao substituir `OnModelCreating`, `base.OnModelCreating` deve ser chamado pela primeira vez; a configuração de substituição deve ser chamada em seguida.</span><span class="sxs-lookup"><span data-stu-id="55315-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="55315-176">O EF Core geralmente tem uma política last-one-wins para a configuração.</span><span class="sxs-lookup"><span data-stu-id="55315-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="55315-177">Por exemplo, se o `ToTable` método para um tipo de entidade é chamado pela primeira vez com o nome de uma tabela e, em seguida, novamente mais tarde com um nome de tabela diferente, o nome da tabela na segunda chamada é usado.</span><span class="sxs-lookup"><span data-stu-id="55315-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="55315-178">Dados de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="55315-178">Custom user data</span></span>

<span data-ttu-id="55315-179">[Dados de usuário personalizados](xref:security/authentication/add-user-data) há suporte para herdar de `IdentityUser`.</span><span class="sxs-lookup"><span data-stu-id="55315-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="55315-180">É comum para esse tipo de nome `ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="55315-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="55315-181">Use o `ApplicationUser` tipo como um argumento genérico para o contexto:</span><span class="sxs-lookup"><span data-stu-id="55315-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

<span data-ttu-id="55315-182">Não é necessário substituir `OnModelCreating` no `ApplicationDbContext` classe.</span><span class="sxs-lookup"><span data-stu-id="55315-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="55315-183">O EF Core mapeia o `CustomTag` propriedade por convenção.</span><span class="sxs-lookup"><span data-stu-id="55315-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="55315-184">No entanto, o banco de dados precisa ser atualizado para criar um novo `CustomTag` coluna.</span><span class="sxs-lookup"><span data-stu-id="55315-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="55315-185">Para criar a coluna, adicione uma migração e, em seguida, atualize o banco de dados, conforme descrito em [identidade e migrações do EF Core](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="55315-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="55315-186">Atualização `Startup.ConfigureServices` para usar o novo `ApplicationUser` classe:</span><span class="sxs-lookup"><span data-stu-id="55315-186">Update `Startup.ConfigureServices` to use the new `ApplicationUser` class:</span></span>

::: moniker range=">= aspnetcore-2.1"

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="55315-187">No ASP.NET Core 2.1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor.</span><span class="sxs-lookup"><span data-stu-id="55315-187">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="55315-188">Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="55315-188">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="55315-189">Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="55315-189">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="55315-190">Se o scaffolder de identidade foi usado para adicionar arquivos de identidade para o projeto, remova a chamada para `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="55315-190">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="55315-191">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="55315-191">For more information, see:</span></span>

* [<span data-ttu-id="55315-192">Identidade Scaffold</span><span class="sxs-lookup"><span data-stu-id="55315-192">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="55315-193">Adicionar, baixar e excluir dados de usuário personalizada à identidade</span><span class="sxs-lookup"><span data-stu-id="55315-193">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
        .AddDefaultTokenProviders();
```

::: moniker-end

### <a name="change-the-primary-key-type"></a><span data-ttu-id="55315-194">Alterar o tipo de chave primária</span><span class="sxs-lookup"><span data-stu-id="55315-194">Change the primary key type</span></span>

<span data-ttu-id="55315-195">Uma alteração para o tipo de dados da coluna PK depois que o banco de dados foi criado é um problema em muitos sistemas de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="55315-195">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="55315-196">Alterando a PK normalmente envolve descartar e recriar a tabela.</span><span class="sxs-lookup"><span data-stu-id="55315-196">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="55315-197">Portanto, tipos de chave devem ser especificados na migração inicial quando o banco de dados é criado.</span><span class="sxs-lookup"><span data-stu-id="55315-197">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="55315-198">Siga estas etapas para alterar o tipo de PK:</span><span class="sxs-lookup"><span data-stu-id="55315-198">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="55315-199">Se o banco de dados foi criado antes da alteração de PK, execute `Drop-Database` (PMC) ou `dotnet ef database drop` (CLI do .NET Core) para excluí-lo.</span><span class="sxs-lookup"><span data-stu-id="55315-199">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="55315-200">Depois de confirmar a exclusão do banco de dados, remova a migração inicial com `Remove-Migration` (PMC) ou `dotnet ef migrations remove` (CLI do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="55315-200">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="55315-201">Atualizar o `ApplicationDbContext` classe para derivar de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext`3>.</span><span class="sxs-lookup"><span data-stu-id="55315-201">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext`3>.</span></span> <span data-ttu-id="55315-202">Especifique o novo tipo de chave para `TKey`.</span><span class="sxs-lookup"><span data-stu-id="55315-202">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="55315-203">Por exemplo, para usar um `Guid` tipo de chave:</span><span class="sxs-lookup"><span data-stu-id="55315-203">For example, to use a `Guid` key type:</span></span>

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

    <span data-ttu-id="55315-204">No código anterior, as classes genéricas <xref:Microsoft.AspNetCore.Identity.IdentityUser`1> e <xref:Microsoft.AspNetCore.Identity.IdentityRole`1> deve ser especificado para usar o novo tipo de chave.</span><span class="sxs-lookup"><span data-stu-id="55315-204">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser`1> and <xref:Microsoft.AspNetCore.Identity.IdentityRole`1> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="55315-205">No código anterior, as classes genéricas <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser`1> e <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole`1> deve ser especificado para usar o novo tipo de chave.</span><span class="sxs-lookup"><span data-stu-id="55315-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser`1> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole`1> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="55315-206">`Startup.ConfigureServices` deve ser atualizado para usar o usuário genérico:</span><span class="sxs-lookup"><span data-stu-id="55315-206">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

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

4. <span data-ttu-id="55315-207">Se um personalizado `ApplicationUser` classe está sendo usado, atualize a classe para herdar de `IdentityUser`.</span><span class="sxs-lookup"><span data-stu-id="55315-207">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="55315-208">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="55315-208">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="55315-209">Atualização `ApplicationDbContext` para fazer referência a personalizada `ApplicationUser` classe:</span><span class="sxs-lookup"><span data-stu-id="55315-209">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

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

    <span data-ttu-id="55315-210">Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55315-210">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="55315-211">Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.</span><span class="sxs-lookup"><span data-stu-id="55315-211">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    <span data-ttu-id="55315-212">No ASP.NET Core 2.1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor.</span><span class="sxs-lookup"><span data-stu-id="55315-212">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="55315-213">Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="55315-213">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="55315-214">Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="55315-214">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="55315-215">Se o scaffolder de identidade foi usado para adicionar arquivos de identidade para o projeto, remova a chamada para `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="55315-215">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="55315-216">Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.</span><span class="sxs-lookup"><span data-stu-id="55315-216">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="55315-217">O <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> método aceita um `TKey` tipo que indica o tipo de dados da chave primária.</span><span class="sxs-lookup"><span data-stu-id="55315-217">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="55315-218">Se um personalizado `ApplicationRole` classe está sendo usado, atualize a classe para herdar de `IdentityRole<TKey>`.</span><span class="sxs-lookup"><span data-stu-id="55315-218">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="55315-219">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="55315-219">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="55315-220">Atualização `ApplicationDbContext` para fazer referência a personalizada `ApplicationRole` classe.</span><span class="sxs-lookup"><span data-stu-id="55315-220">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="55315-221">Por exemplo, a classe a seguir faz referência a um personalizado `ApplicationUser` e um personalizado `ApplicationRole`:</span><span class="sxs-lookup"><span data-stu-id="55315-221">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="55315-222">Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55315-222">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="55315-223">Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.</span><span class="sxs-lookup"><span data-stu-id="55315-223">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    <span data-ttu-id="55315-224">No ASP.NET Core 2.1 ou posterior, a identidade é fornecida como uma biblioteca de classes Razor.</span><span class="sxs-lookup"><span data-stu-id="55315-224">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="55315-225">Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="55315-225">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="55315-226">Consequentemente, o código anterior requer uma chamada para <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="55315-226">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="55315-227">Se o scaffolder de identidade foi usado para adicionar arquivos de identidade para o projeto, remova a chamada para `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="55315-227">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="55315-228">Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55315-228">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="55315-229">Tipo de dados da chave primária é inferido, analisando o <xref:Microsoft.EntityFrameworkCore.DbContext> objeto.</span><span class="sxs-lookup"><span data-stu-id="55315-229">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="55315-230">Registrar a classe de contexto do banco de dados personalizados ao adicionar o serviço de identidade no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55315-230">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="55315-231">O <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> método aceita um `TKey` tipo que indica o tipo de dados da chave primária.</span><span class="sxs-lookup"><span data-stu-id="55315-231">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="55315-232">Adicionar propriedades de navegação</span><span class="sxs-lookup"><span data-stu-id="55315-232">Add navigation properties</span></span>

<span data-ttu-id="55315-233">Alterar a configuração de modelo para relações pode ser mais difícil do que fazendo outras alterações.</span><span class="sxs-lookup"><span data-stu-id="55315-233">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="55315-234">Deve-se ter cuidado para substituir as relações existentes em vez de criar novas relações adicionais.</span><span class="sxs-lookup"><span data-stu-id="55315-234">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="55315-235">Em particular, a relação alterada deve especificar o mesmo (FK) propriedade de chave estrangeira como a relação existente.</span><span class="sxs-lookup"><span data-stu-id="55315-235">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="55315-236">Por exemplo, a relação entre `Users` e `UserClaims` é, por padrão, especificada da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="55315-236">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

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

<span data-ttu-id="55315-237">A FK para essa relação é especificada como o `UserClaim.UserId` propriedade.</span><span class="sxs-lookup"><span data-stu-id="55315-237">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="55315-238">`HasMany` e `WithOne` são chamados sem argumentos para criar a relação sem propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="55315-238">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="55315-239">Adicionar uma propriedade de navegação `ApplicationUser` que permite que associado `UserClaims` seja referenciado do usuário:</span><span class="sxs-lookup"><span data-stu-id="55315-239">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="55315-240">O `TKey` para `IdentityUserClaim<TKey>` é o tipo especificado para o PK de usuários.</span><span class="sxs-lookup"><span data-stu-id="55315-240">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="55315-241">Nesse caso, `TKey` é `string` porque os padrões estão sendo usados.</span><span class="sxs-lookup"><span data-stu-id="55315-241">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="55315-242">Ele tem **não** o tipo de PK para o `UserClaim` tipo de entidade.</span><span class="sxs-lookup"><span data-stu-id="55315-242">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="55315-243">Agora que existe a propriedade de navegação, ele deve ser configurado no `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="55315-243">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

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

<span data-ttu-id="55315-244">Observe que a relação é configurada exatamente como ele era antes, apenas com uma propriedade de navegação especificada na chamada para `HasMany`.</span><span class="sxs-lookup"><span data-stu-id="55315-244">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="55315-245">As propriedades de navegação só existem no modelo do EF, não o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="55315-245">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="55315-246">Como a FK para a relação não foi alterado, esse tipo de alteração de modelo não exige o banco de dados a ser atualizada.</span><span class="sxs-lookup"><span data-stu-id="55315-246">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="55315-247">Isso pode ser verificado com a adição de uma migração após fazer a alteração.</span><span class="sxs-lookup"><span data-stu-id="55315-247">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="55315-248">O `Up` e `Down` métodos estão vazios.</span><span class="sxs-lookup"><span data-stu-id="55315-248">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="55315-249">Adicionar todas as propriedades de navegação do usuário</span><span class="sxs-lookup"><span data-stu-id="55315-249">Add all User navigation properties</span></span>

<span data-ttu-id="55315-250">O exemplo a seguir usando a seção acima como orientação, configura as propriedades de navegação unidirecional para todas as relações em usuário:</span><span class="sxs-lookup"><span data-stu-id="55315-250">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

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

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="55315-251">Adicionar propriedades de navegação do usuário e a função</span><span class="sxs-lookup"><span data-stu-id="55315-251">Add User and Role navigation properties</span></span>

<span data-ttu-id="55315-252">Usando a seção acima como orientação, o exemplo a seguir configura as propriedades de navegação para todas as relações no usuário e a função:</span><span class="sxs-lookup"><span data-stu-id="55315-252">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

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

<span data-ttu-id="55315-253">Notas:</span><span class="sxs-lookup"><span data-stu-id="55315-253">Notes:</span></span>

* <span data-ttu-id="55315-254">Este exemplo também inclui o `UserRole` entidade, que é necessário para navegar pela relação muitos-para-muitos dos usuários às funções de ingresso.</span><span class="sxs-lookup"><span data-stu-id="55315-254">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="55315-255">Lembre-se de alterar os tipos das propriedades de navegação de refletir isso `ApplicationXxx` tipos agora estão sendo usados em vez de `IdentityXxx` tipos.</span><span class="sxs-lookup"><span data-stu-id="55315-255">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="55315-256">Lembre-se de usar o `ApplicationXxx` no genérico `ApplicationContext` definição.</span><span class="sxs-lookup"><span data-stu-id="55315-256">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="55315-257">Adicionar todas as propriedades de navegação</span><span class="sxs-lookup"><span data-stu-id="55315-257">Add all navigation properties</span></span>

<span data-ttu-id="55315-258">Usando a seção acima como orientação, o exemplo a seguir configura as propriedades de navegação para todas as relações em todos os tipos de entidade:</span><span class="sxs-lookup"><span data-stu-id="55315-258">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

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

### <a name="use-composite-keys"></a><span data-ttu-id="55315-259">Usar chaves compostas</span><span class="sxs-lookup"><span data-stu-id="55315-259">Use composite keys</span></span>

<span data-ttu-id="55315-260">As seções anteriores demonstrou como alterar o tipo da chave usada no modelo de identidade.</span><span class="sxs-lookup"><span data-stu-id="55315-260">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="55315-261">Alterando o modelo de chave de identidade para usar chaves compostas não é suportado nem recomendado.</span><span class="sxs-lookup"><span data-stu-id="55315-261">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="55315-262">Usar uma chave composta com identidade envolve a alteração como o código do Gerenciador de identidade interage com o modelo.</span><span class="sxs-lookup"><span data-stu-id="55315-262">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="55315-263">Essa personalização está além do escopo deste documento.</span><span class="sxs-lookup"><span data-stu-id="55315-263">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="55315-264">Alterar nomes de tabela/coluna e as facetas</span><span class="sxs-lookup"><span data-stu-id="55315-264">Change table/column names and facets</span></span>

<span data-ttu-id="55315-265">Para alterar os nomes das tabelas e colunas, chame `base.OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="55315-265">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="55315-266">Em seguida, adicione a configuração para substituir qualquer um dos padrões.</span><span class="sxs-lookup"><span data-stu-id="55315-266">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="55315-267">Por exemplo, para alterar o nome de todas as tabelas de identidade:</span><span class="sxs-lookup"><span data-stu-id="55315-267">For example, to change the name of all the Identity tables:</span></span>

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

<span data-ttu-id="55315-268">Esses exemplos usam os tipos de identidade padrão.</span><span class="sxs-lookup"><span data-stu-id="55315-268">These examples use the default Identity types.</span></span> <span data-ttu-id="55315-269">Se usar um tipo de aplicativo, como `ApplicationUser`, configurar esse tipo em vez do tipo padrão.</span><span class="sxs-lookup"><span data-stu-id="55315-269">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="55315-270">O exemplo a seguir altera alguns nomes de coluna:</span><span class="sxs-lookup"><span data-stu-id="55315-270">The following example changes some column names:</span></span>

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

<span data-ttu-id="55315-271">Alguns tipos de colunas de banco de dados podem ser configurados com determinados *facetas* (por exemplo, o máximo `string` comprimento permitido).</span><span class="sxs-lookup"><span data-stu-id="55315-271">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="55315-272">O exemplo a seguir define os comprimentos máximos da coluna para várias `string` propriedades no modelo:</span><span class="sxs-lookup"><span data-stu-id="55315-272">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

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

### <a name="map-to-a-different-schema"></a><span data-ttu-id="55315-273">Mapear para um esquema diferente</span><span class="sxs-lookup"><span data-stu-id="55315-273">Map to a different schema</span></span>

<span data-ttu-id="55315-274">Esquemas podem se comportar de maneira diferente em provedores de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="55315-274">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="55315-275">Para o SQL Server, o padrão é criar todas as tabelas na *dbo* esquema.</span><span class="sxs-lookup"><span data-stu-id="55315-275">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="55315-276">As tabelas podem ser criadas em um esquema diferente.</span><span class="sxs-lookup"><span data-stu-id="55315-276">The tables can be created in a different schema.</span></span> <span data-ttu-id="55315-277">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="55315-277">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="55315-278">Carregamento lento</span><span class="sxs-lookup"><span data-stu-id="55315-278">Lazy loading</span></span>

<span data-ttu-id="55315-279">Nesta seção, o suporte para proxies de carregamento lento no modelo de identidade é adicionado.</span><span class="sxs-lookup"><span data-stu-id="55315-279">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="55315-280">Carregamento lento é útil, pois ele permite que as propriedades de navegação a ser usada sem primeiro garantir que eles são carregados.</span><span class="sxs-lookup"><span data-stu-id="55315-280">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="55315-281">Tipos de entidade podem ser feitos adequados para carregamento lento de diversas maneiras, conforme descrito na [documentação do EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="55315-281">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="55315-282">Para simplificar, use proxies de carregamento lento, que requer:</span><span class="sxs-lookup"><span data-stu-id="55315-282">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="55315-283">Instalação dos [entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) pacote.</span><span class="sxs-lookup"><span data-stu-id="55315-283">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="55315-284">Uma chamada para <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> dentro de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>.</span><span class="sxs-lookup"><span data-stu-id="55315-284">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>.</span></span>
* <span data-ttu-id="55315-285">Tipos de entidade pública com `public virtual` propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="55315-285">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="55315-286">O exemplo a seguir demonstra a chamada `UseLazyLoadingProxies` em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55315-286">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="55315-287">Consulte os exemplos anteriores para obter orientação sobre como adicionar propriedades de navegação para os tipos de entidade.</span><span class="sxs-lookup"><span data-stu-id="55315-287">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="55315-288">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="55315-288">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
