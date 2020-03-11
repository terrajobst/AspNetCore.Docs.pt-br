---
title: Migrar da autenticação de associação do ASP.NET para a identidade do ASP.NET Core 2,0
author: isaac2004
description: Saiba como migrar aplicativos ASP.NET existentes usando a autenticação de associação para ASP.NET Core identidade 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659239"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="aa0e9-103">Migrar da autenticação de associação do ASP.NET para a identidade do ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="aa0e9-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="aa0e9-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="aa0e9-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="aa0e9-105">Este artigo demonstra como migrar o esquema de banco de dados para aplicativos ASP.NET usando a autenticação de associação para ASP.NET Core identidade 2,0.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="aa0e9-106">Este documento fornece as etapas necessárias para migrar o esquema de banco de dados para aplicativos baseados em associação do ASP.NET para o esquema de banco de dados usado para ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="aa0e9-107">Para obter mais informações sobre como migrar da autenticação baseada em associação do ASP.NET para ASP.NET Identity, consulte [migrar um aplicativo existente da associação do SQL para ASP.net Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="aa0e9-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="aa0e9-108">Para obter mais informações sobre a identidade de ASP.NET Core, consulte [introdução à identidade em ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="aa0e9-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="aa0e9-109">Revisão do esquema de associação</span><span class="sxs-lookup"><span data-stu-id="aa0e9-109">Review of Membership schema</span></span>

<span data-ttu-id="aa0e9-110">Antes do ASP.NET 2,0, os desenvolvedores eram transtarefados na criação de todo o processo de autenticação e autorização para seus aplicativos.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="aa0e9-111">Com o ASP.NET 2,0, a associação foi introduzida, fornecendo uma solução padronizada para lidar com a segurança em aplicativos ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="aa0e9-112">Agora, os desenvolvedores conseguiram inicializar um esquema em um banco de dados SQL Server com o comando [Aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="aa0e9-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="aa0e9-113">Depois de executar esse comando, as tabelas a seguir foram criadas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-113">After running this command, the following tables were created in the database.</span></span>

  ![Tabelas de associação](identity/_static/membership-tables.png)

<span data-ttu-id="aa0e9-115">Para migrar aplicativos existentes para ASP.NET Core identidade 2,0, os dados nessas tabelas precisam ser migrados para as tabelas usadas pelo novo esquema de identidade.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="aa0e9-116">Esquema ASP.NET Core Identity 2,0</span><span class="sxs-lookup"><span data-stu-id="aa0e9-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="aa0e9-117">ASP.NET Core 2,0 segue o princípio de [identidade](/aspnet/identity/index) introduzido no ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="aa0e9-118">Embora o princípio seja compartilhado, a implementação entre as estruturas é diferente, mesmo entre as versões do ASP.NET Core (consulte [migrar autenticação e identidade para ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="aa0e9-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="aa0e9-119">A maneira mais rápida de exibir o esquema para ASP.NET Core identidade 2,0 é criar um novo aplicativo ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="aa0e9-120">Siga estas etapas no Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="aa0e9-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="aa0e9-121">Selecione **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="aa0e9-122">Crie um novo projeto de **aplicativo Web ASP.NET Core** chamado *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="aa0e9-123">Selecione **ASP.NET Core 2,0** na lista suspensa e, em seguida, selecione **aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="aa0e9-124">Este modelo produz um aplicativo [Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="aa0e9-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="aa0e9-125">Antes de clicar em **OK**, clique em **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="aa0e9-126">Escolha **contas de usuário individuais** para os modelos de identidade.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="aa0e9-127">Por fim, clique em **OK**e em **OK**.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="aa0e9-128">O Visual Studio cria um projeto usando o modelo de identidade ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="aa0e9-129">Selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **console do Gerenciador de pacotes** para abrir a janela do console do Gerenciador de **pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="aa0e9-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="aa0e9-130">Navegue até a raiz do projeto no PMC e execute o comando `Update-Database` [Core do Entity Framework (EF)](/ef/core) .</span><span class="sxs-lookup"><span data-stu-id="aa0e9-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="aa0e9-131">ASP.NET Core identidade 2,0 usa EF Core para interagir com o banco de dados que está armazenando os dados de autenticação.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="aa0e9-132">Para que o aplicativo recém-criado funcione, é necessário haver um banco de dados para armazenar os mesmos.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="aa0e9-133">Depois de criar um novo aplicativo, a maneira mais rápida de inspecionar o esquema em um ambiente de banco de dados é criar o banco de dados usando [EF Core migrações](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="aa0e9-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="aa0e9-134">Esse processo cria um banco de dados, seja localmente ou em outro lugar, que imita esse esquema.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="aa0e9-135">Examine a documentação anterior para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="aa0e9-136">EF Core comandos usam a cadeia de conexão para o banco de dados especificado em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="aa0e9-137">A cadeia de conexão a seguir destina-se a um banco de dados no *localhost* denominado *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="aa0e9-138">Nessa configuração, EF Core é configurado para usar a cadeia de conexão `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="aa0e9-139">Selecione **exibir** > **pesquisador de objetos do SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="aa0e9-140">Expanda o nó correspondente ao nome do banco de dados especificado na propriedade `ConnectionStrings:DefaultConnection` de *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="aa0e9-141">O comando `Update-Database` criou o banco de dados especificado com o esquema e qualquer dado necessário para a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="aa0e9-142">A imagem a seguir ilustra a estrutura de tabela criada com as etapas anteriores.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Tabelas de identidade](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="aa0e9-144">Migrar o esquema</span><span class="sxs-lookup"><span data-stu-id="aa0e9-144">Migrate the schema</span></span>

<span data-ttu-id="aa0e9-145">Há diferenças sutis nas estruturas de tabela e nos campos para a associação e a identidade de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="aa0e9-146">O padrão mudou substancialmente para autenticação/autorização com aplicativos ASP.NET e ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="aa0e9-147">Os objetos de chave que ainda são usados com a identidade são *usuários* e *funções*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="aa0e9-148">Aqui estão mapeando tabelas para *usuários*, *funções*e *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="aa0e9-149">Usuários</span><span class="sxs-lookup"><span data-stu-id="aa0e9-149">Users</span></span>

|<span data-ttu-id="aa0e9-150">*<br>de identidade (dbo. AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="aa0e9-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="aa0e9-151">*<br>de associação (dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="aa0e9-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="aa0e9-152">**Nome do Campo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-152">**Field Name**</span></span>                 |<span data-ttu-id="aa0e9-153">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-153">**Type**</span></span>|<span data-ttu-id="aa0e9-154">**Nome do Campo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-154">**Field Name**</span></span>                                    |<span data-ttu-id="aa0e9-155">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="aa0e9-156">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="aa0e9-157">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="aa0e9-158">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="aa0e9-159">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="aa0e9-160">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="aa0e9-161">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="aa0e9-162">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="aa0e9-163">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="aa0e9-164">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="aa0e9-165">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="aa0e9-166">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="aa0e9-167">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="aa0e9-168">bit</span><span class="sxs-lookup"><span data-stu-id="aa0e9-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="aa0e9-169">bit</span><span class="sxs-lookup"><span data-stu-id="aa0e9-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="aa0e9-170">Nem todos os mapeamentos de campo se assemelham a relações um-para-um de associação a ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="aa0e9-171">A tabela anterior usa o esquema de usuário de associação padrão e o mapeia para o esquema de identidade ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="aa0e9-172">Todos os outros campos personalizados que foram usados para associação precisam ser mapeados manualmente.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="aa0e9-173">Nesse mapeamento, não há nenhum mapa para senhas, pois os critérios de senha e os Salts de senha não são migrados entre os dois.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="aa0e9-174">**É recomendável deixar a senha como nula e solicitar que os usuários redefinam suas senhas.**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="aa0e9-175">Em ASP.NET Core identidade, `LockoutEnd` deve ser definido como alguma data no futuro se o usuário estiver bloqueado. Isso é mostrado no script de migração.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="aa0e9-176">Funções</span><span class="sxs-lookup"><span data-stu-id="aa0e9-176">Roles</span></span>

|<span data-ttu-id="aa0e9-177">*<br>de identidade (dbo. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="aa0e9-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="aa0e9-178">*<br>de associação (dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="aa0e9-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="aa0e9-179">**Nome do Campo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-179">**Field Name**</span></span>                 |<span data-ttu-id="aa0e9-180">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-180">**Type**</span></span>|<span data-ttu-id="aa0e9-181">**Nome do Campo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-181">**Field Name**</span></span>   |<span data-ttu-id="aa0e9-182">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="aa0e9-183">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-183">string</span></span>  |`RoleId`         | <span data-ttu-id="aa0e9-184">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="aa0e9-185">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-185">string</span></span>  |`RoleName`       | <span data-ttu-id="aa0e9-186">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="aa0e9-187">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="aa0e9-188">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="aa0e9-189">Funções do usuário</span><span class="sxs-lookup"><span data-stu-id="aa0e9-189">User Roles</span></span>

|<span data-ttu-id="aa0e9-190">*<br>de identidade (dbo. AspNetUserRoles*</span><span class="sxs-lookup"><span data-stu-id="aa0e9-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="aa0e9-191">*<br>de associação (dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="aa0e9-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="aa0e9-192">**Nome do Campo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-192">**Field Name**</span></span>           |<span data-ttu-id="aa0e9-193">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-193">**Type**</span></span>  |<span data-ttu-id="aa0e9-194">**Nome do Campo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-194">**Field Name**</span></span>|<span data-ttu-id="aa0e9-195">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="aa0e9-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="aa0e9-196">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-196">string</span></span>    |`RoleId`      |<span data-ttu-id="aa0e9-197">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="aa0e9-198">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-198">string</span></span>    |`UserId`      |<span data-ttu-id="aa0e9-199">string</span><span class="sxs-lookup"><span data-stu-id="aa0e9-199">string</span></span>                     |

<span data-ttu-id="aa0e9-200">Referencie as tabelas de mapeamento anteriores ao criar um script de migração para *usuários* e *funções*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="aa0e9-201">O exemplo a seguir pressupõe que você tenha dois bancos de dados em um servidor de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="aa0e9-202">Um banco de dados contém o esquema de associação do ASP.NET existente.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="aa0e9-203">O outro banco de dados *CoreIdentitySample* foi criado usando as etapas descritas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="aa0e9-204">Os comentários são incluídos embutidos para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-204">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="aa0e9-205">Após a conclusão do script anterior, o aplicativo de identidade ASP.NET Core criado anteriormente é preenchido com usuários de associação.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="aa0e9-206">Os usuários precisam alterar suas senhas antes de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="aa0e9-207">Se o sistema de associação tiver usuários com nomes de usuário que não corresponderam a seu endereço de email, as alterações serão necessárias para o aplicativo criado anteriormente para acomodar isso.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="aa0e9-208">O modelo padrão espera que `UserName` e `Email` sejam iguais.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="aa0e9-209">Para situações em que elas são diferentes, o processo de logon precisa ser modificado para usar `UserName` em vez de `Email`.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="aa0e9-210">Na `PageModel` da página de logon, localizada em *Pages\Account\Login.cshtml.cs*, remova o atributo `[EmailAddress]` da propriedade *email* .</span><span class="sxs-lookup"><span data-stu-id="aa0e9-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="aa0e9-211">Renomeie-o como *username*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-211">Rename it to *UserName*.</span></span> <span data-ttu-id="aa0e9-212">Isso requer uma alteração sempre que `EmailAddress` for mencionado, na *exibição* e *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="aa0e9-213">O resultado se parece com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="aa0e9-213">The result looks like the following:</span></span>

 ![Logon fixo](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="aa0e9-215">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="aa0e9-215">Next steps</span></span>

<span data-ttu-id="aa0e9-216">Neste tutorial, você aprendeu a portar os usuários da associação do SQL para ASP.NET Core identidade 2,0.</span><span class="sxs-lookup"><span data-stu-id="aa0e9-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="aa0e9-217">Para obter mais informações sobre a identidade de ASP.NET Core, consulte [introdução à identidade](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="aa0e9-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
