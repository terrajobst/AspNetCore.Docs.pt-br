---
title: Provedores de armazenamento personalizados para ASP.NET Core Identity
author: ardalis
description: Saiba como configurar provedores de armazenamento personalizados para ASP.NET Core Identity.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: ccd56d0c15639e1ad29094e947f8055702ee2264
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833664"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Provedores de armazenamento personalizados para ASP.NET Core Identity

Por [Steve Smith](https://ardalis.com/)

O ASP.NET Core Identity é um sistema extensível que permite que você crie um provedor de armazenamento personalizado e conectá-lo ao seu aplicativo. Este tópico descreve como criar um provedor de armazenamento personalizado para o ASP.NET Core Identity. Ele aborda os conceitos importantes para a criação de seu próprio provedor de armazenamento, mas não é um passo a passo.

[Exibir ou baixar amostra do GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Introdução

Por padrão, o sistema de identidade do ASP.NET Core armazena informações de usuário em um banco de dados do SQL Server usando o Entity Framework Core. Para muitos aplicativos, essa abordagem funciona bem. No entanto, você pode preferir usar um mecanismo de persistência diferente ou esquema de dados. Por exemplo:

* Você usa [Azure Table Storage](/azure/storage/) ou outro armazenamento de dados.
* As tabelas de banco de dados têm uma estrutura diferente. 
* Talvez você queira usar uma abordagem de acesso de dados diferentes, como [Dapper](https://github.com/StackExchange/Dapper). 

Em cada um desses casos, você pode escrever um provedor personalizado para seu mecanismo de armazenamento e conecte esse provedor ao seu aplicativo.

O ASP.NET Core Identity é incluído nos modelos de projeto no Visual Studio com a opção "Contas de usuário individuais".

Ao usar a CLI do .NET Core, adicione `-au Individual`:

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>A arquitetura do ASP.NET Core Identity

Identidade do ASP.NET Core consiste em classes chamado gerentes e repositórios. *Gerenciadores de* são classes de alto nível que um desenvolvedor de aplicativo usa para executar operações, como a criação de um usuário de identidade. *Repositórios* são classes de nível inferior que especificam como entidades, como usuários e funções, são persistentes. Repositórios seguem o padrão de repositório e estão intimamente acoplados com o mecanismo de persistência. Os gerentes são separados dos armazenamentos, que significa que você pode substituir o mecanismo de persistência sem alterar o código do aplicativo (com exceção de configuração).

O diagrama a seguir mostra como um aplicativo web interage com os gerentes, enquanto os armazenamentos de interagem com a camada de acesso a dados.

![Aplicativos ASP.NET Core trabalhar com os gerentes (por exemplo, 'UserManager', 'RoleManager'). Gerenciadores de trabalhar com repositórios (por exemplo, ' UserStore') que se comunicam com uma fonte de dados usando uma biblioteca como o Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Para criar um provedor de armazenamento personalizado, crie a fonte de dados, a camada de acesso a dados e as classes de armazenamento que interagem com esta camada de acesso de dados (as caixas verdes e cinza no diagrama acima). Você não precisa personalizar os gerentes ou seu código de aplicativo que interage com eles (as caixas azuis acima).

Ao criar uma nova instância da `UserManager` ou `RoleManager` você fornecer o tipo da classe de usuário e passar uma instância da classe de armazenamento como um argumento. Essa abordagem permite que você conecte suas classes personalizadas no ASP.NET Core. 

[Reconfigurar o aplicativo para usar o novo provedor de armazenamento](#reconfigure-app-to-use-a-new-storage-provider) mostra como instanciar `UserManager` e `RoleManager` com um repositório personalizado.

## <a name="aspnet-core-identity-stores-data-types"></a>Tipos de dados de armazenamentos de identidade do ASP.NET Core

[ASP.NET Core Identity](https://github.com/aspnet/identity) tipos de dados são detalhados nas seções a seguir:

### <a name="users"></a>Usuários

Usuários registrados do seu site da web. O [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) tipo pode ser estendido ou usado como um exemplo para seu próprio tipo personalizado. Você não precisa herdar de um tipo específico para implementar sua própria solução de armazenamento de identidade personalizada.

### <a name="user-claims"></a>Declarações de usuário

Um conjunto de instruções (ou [declarações](/dotnet/api/system.security.claims.claim)) sobre o usuário que representam a identidade do usuário. Pode permitir que uma expressão maior da identidade do usuário que pode ser obtido por meio de funções.

### <a name="user-logins"></a>Logons de usuário

Informações sobre o provedor de autenticação externa (como Facebook ou uma conta da Microsoft) para usar ao fazer logon em um usuário. [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Funções

Grupos de autorização para seu site. Inclui o nome da função Id e a função (como "Admin" ou "Employee"). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>A camada de acesso a dados

Este tópico pressupõe que você esteja familiarizado com o mecanismo de persistência que você pretende usar e como criar entidades para esse mecanismo. Este tópico não fornece detalhes sobre como criar os repositórios ou classes de acesso a dados; Ele fornece algumas sugestões sobre decisões de design ao trabalhar com o ASP.NET Core Identity.

Você tem liberdade ao projetar a camada de acesso a dados para um provedor de repositório personalizado. Você só precisará criar mecanismos de persistência para os recursos que você pretende usar em seu aplicativo. Por exemplo, se você não estiver usando as funções em seu aplicativo, você não precisa criar armazenamento para funções ou associações de função de usuário. Sua tecnologia e a infraestrutura existente podem exigir uma estrutura que é muito diferente da implementação padrão do ASP.NET Core Identity. Na camada de acesso a dados, você deve fornecer a lógica para trabalhar com a estrutura da sua implementação de armazenamento.

A camada de acesso a dados fornece a lógica para salvar os dados de identidade do ASP.NET Core em uma fonte de dados. Camada de acesso a dados para o seu provedor de armazenamento personalizado pode incluir as seguintes classes para armazenar informações de usuário e a função.

### <a name="context-class"></a>Classe de contexto

Encapsula as informações para conectar-se ao mecanismo de persistência e executar consultas. Várias classes de dados requerem uma instância dessa classe, normalmente é fornecido por meio da injeção de dependência. [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Armazenamento de usuário

Armazena e recupera informações de usuário (por exemplo, o hash de nome e a senha do usuário). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Armazenamento de função

Armazena e recupera informações de função (como o nome da função). [Exemplo](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Armazenamento de UserClaims

Armazena e recupera informações de declaração de usuário (como o tipo de declaração e o valor). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Armazenamento de UserLogins

Armazena e recupera informações de logon do usuário (como um provedor de autenticação externa). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Armazenamento de UserRole

Armazena e recupera a quais funções são atribuídas a quais usuários. [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**DICA:** Implemente apenas as classes que você pretende usar em seu aplicativo.

Em classes de acesso a dados, forneça o código para executar operações de dados para o mecanismo de persistência. Por exemplo, dentro de um provedor personalizado, você pode ter o código a seguir para criar um novo usuário na *armazenar* classe:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

A lógica de implementação para criar o usuário está no `_usersTable.CreateAsync` método, mostrado abaixo.

## <a name="customize-the-user-class"></a>Personalizar a classe de usuário

Ao implementar um provedor de armazenamento, crie uma classe de usuário que é equivalente à [classe IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

No mínimo, sua classe de usuário deve incluir um `Id` e um `UserName` propriedade.

O `IdentityUser` classe define as propriedades que o `UserManager` chamadas ao executar operações de solicitadas. O tipo de padrão de `Id` propriedade é uma cadeia de caracteres, mas você pode herdar de `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` e especifique um tipo diferente. O framework espera que a implementação de armazenamento para lidar com conversões de tipo de dados.

## <a name="customize-the-user-store"></a>Personalizar o repositório do usuário

Criar um `UserStore` classe que fornece os métodos para todas as operações de dados no usuário. Essa classe é equivalente a [UserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) classe. No seu `UserStore` classe, implemente `IUserStore<TUser>` e as interfaces opcionais necessárias. Você selecione quais interfaces opcionais para implementar com base na funcionalidade fornecida no seu aplicativo.

### <a name="optional-interfaces"></a>Interfaces opcionais

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

As interfaces opcionais herdam `IUserStore<TUser>`. Você pode ver que um usuário de exemplo parcialmente implementada armazenar na [aplicativo de exemplo](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).

Dentro de `UserStore` classe, que você usar as classes de acesso de dados que você criou para executar operações. Eles são passados usando a injeção de dependência. Por exemplo, no SQL Server com a implementação do Dapper, o `UserStore` classe tem o `CreateAsync` método que usa uma instância de `DapperUsersTable` para inserir um novo registro:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Interfaces a serem implementadas durante a personalização armazenamento de usuário

* **IUserStore**  
 O [IUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface é a única interface, você deve implementar no repositório do usuário. Define métodos para criar, atualizar, excluir e recuperar os usuários.
* **IUserClaimStore**  
 O [IUserClaimStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface define os métodos a implementar para habilitar declarações do usuário. Ela contém métodos para adicionar, remover e recuperando as declarações de usuário.
* **IUserLoginStore**  
 O [IUserLoginStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) define os métodos a implementar para habilitar provedores de autenticação externa. Ela contém métodos para adicionar, remover e recuperar os logons de usuário e um método para recuperar um usuário com base nas informações de logon.
* **IUserRoleStore**  
 O [IUserRoleStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface define os métodos a implementar para mapear um usuário a uma função. Ela contém métodos para adicionar, remover e recuperar funções de usuário e um método para verificar se um usuário está atribuído a uma função.
* **IUserPasswordStore**  
 O [IUserPasswordStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface define os métodos a implementar para persistir as senhas hash. Ela contém métodos para obter e definir a senha de hash e um método que indica se o usuário tiver definido uma senha.
* **IUserSecurityStampStore**  
 O [IUserSecurityStampStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface define os métodos a implementar para usar um carimbo de segurança para que indica se as informações da conta do usuário foi alterado. Esse carimbo é atualizado quando um usuário altera a senha ou adiciona ou remove logons. Ela contém métodos para obter e definir o carimbo de segurança.
* **IUserTwoFactorStore**  
 O [IUserTwoFactorStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface define os métodos a implementar para dar suporte à autenticação de dois fatores. Ela contém métodos para obter e definir se a autenticação de dois fatores é ativada para um usuário.
* **IUserPhoneNumberStore**  
 O [IUserPhoneNumberStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface define os métodos a implementar para armazenar números de telefone do usuário. Ela contém métodos para obter e definir o número de telefone e se o número de telefone foi confirmado.
* **IUserEmailStore**  
 O [IUserEmailStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface define os métodos a implementar para armazenar endereços de email do usuário. Ela contém métodos para obter e definir o endereço de email e se o email for confirmado.
* **IUserLockoutStore**  
 O [IUserLockoutStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface define os métodos a implementar para armazenar informações sobre como bloquear uma conta. Ela contém métodos para controlar as tentativas de acesso com falha e bloqueios.
* **IQueryableUserStore**  
 O [IQueryableUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface define os membros que você pode implementar para fornecer um repositório de usuários que podem ser consultados.

Você pode implementar apenas as interfaces que são necessários em seu aplicativo. Por exemplo:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, IdentityUserLogin e IdentityUserRole

O `Microsoft.AspNet.Identity.EntityFramework` namespace contém implementações do [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), e [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes. Se você estiver usando esses recursos, você talvez queira criar suas próprias versões dessas classes e definir as propriedades para seu aplicativo. No entanto, às vezes, é mais eficiente para não carregar essas entidades na memória ao executar operações básicas (como adicionar ou remover a declaração do usuário). Em vez disso, as classes de armazenamento de back-end podem executar essas operações diretamente na fonte de dados. Por exemplo, o `UserStore.GetClaimsAsync` método pode chamar o `userClaimTable.FindByUserId(user.Id)` método para executar uma consulta em que diretamente da tabela e retorna uma lista de declarações.

## <a name="customize-the-role-class"></a>Personalizar a classe de função

Ao implementar um provedor de armazenamento de função, você pode criar um tipo de função personalizada. Ele não precisa implementar uma interface específica, mas ele deve ter uma `Id` e, normalmente terá uma `Name` propriedade.

A seguir está um exemplo de classe de função:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Personalizar o repositório da função

Você pode criar um `RoleStore` classe que fornece os métodos para todas as operações de dados em funções. Essa classe é equivalente a [alteração do RoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) classe. No `RoleStore` classe, você implementa o `IRoleStore<TRole>` e, opcionalmente, o `IQueryableRoleStore<TRole>` interface.

* **IRoleStore&lt;TRole&gt;**  
 O [IRoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface define os métodos a implementar na classe de repositório de função. Ela contém métodos para criar, atualizar, excluir e recuperar funções.
* **RoleStore&lt;TRole&gt;**  
 Para personalizar `RoleStore`, crie uma classe que implementa o `IRoleStore<TRole>` interface. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Reconfigurar o aplicativo para usar um novo provedor de armazenamento

Depois de implementar um provedor de armazenamento, você pode configurar seu aplicativo para usá-lo. Se seu aplicativo usado o provedor padrão, substitua-o com o provedor personalizado.

1. Remover o `Microsoft.AspNetCore.EntityFramework.Identity` pacote do NuGet.
1. Se o provedor de armazenamento reside em um projeto separado ou um pacote, adicione uma referência a ele.
1. Substitua todas as referências a `Microsoft.AspNetCore.EntityFramework.Identity` com o uso de uma instrução para o namespace do seu provedor de armazenamento.
1. No `ConfigureServices` método, altere o `AddIdentity` método usar seus tipos personalizados. Você pode criar seus próprios métodos de extensão para essa finalidade. Ver [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) para obter um exemplo.
1. Se você estiver usando as funções, atualize o `RoleManager` para usar seu `RoleStore` classe.
1. Atualize a cadeia de caracteres de conexão e as credenciais para a configuração de seu aplicativo.

Exemplo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Referências

* [Provedores de armazenamento personalizados para a identidade do ASP.NET 4.x](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/aspnet/identity) &ndash; neste repositório inclui links para a comunidade mantida provedores de armazenamento.
