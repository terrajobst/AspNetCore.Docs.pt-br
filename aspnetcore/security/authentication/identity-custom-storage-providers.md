---
title: Provedores de armazenamento personalizados para identidade de ASP.NET Core
author: ardalis
description: Saiba como configurar provedores de armazenamento personalizados para ASP.NET Core identidade.
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 70951085474d88fd57f1b1496a41adcda520b91f
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829147"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Provedores de armazenamento personalizados para identidade de ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

ASP.NET Core identidade é um sistema extensível que permite criar um provedor de armazenamento personalizado e conectá-lo ao seu aplicativo. Este tópico descreve como criar um provedor de armazenamento personalizado para ASP.NET Core identidade. Ele aborda os conceitos importantes para criar seu próprio provedor de armazenamento, mas não é um passo a passo.

[Exibir ou baixar amostra do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Introdução

Por padrão, o sistema de identidade ASP.NET Core armazena informações de usuário em um banco de dados SQL Server usando Entity Framework Core. Para muitos aplicativos, essa abordagem funciona bem. No entanto, você pode preferir usar um mecanismo de persistência ou esquema de dados diferente. Por exemplo:

* Você usa o [armazenamento de tabelas do Azure](/azure/storage/) ou outro armazenamento de dados.
* Suas tabelas de banco de dados têm uma estrutura diferente. 
* Talvez você queira usar uma abordagem de acesso a dados diferente, como [Dapper](https://github.com/StackExchange/Dapper). 

Em cada um desses casos, você pode escrever um provedor personalizado para seu mecanismo de armazenamento e conectar esse provedor em seu aplicativo.

ASP.NET Core identidade está incluída nos modelos de projeto no Visual Studio com a opção "contas de usuário individuais".

Ao usar o CLI do .NET Core, adicione `-au Individual`:

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>A arquitetura de identidade ASP.NET Core

ASP.NET Core identidade consiste em classes chamadas gerentes e lojas. *Os gerentes* são classes de alto nível que o desenvolvedor de aplicativo usa para executar operações, como a criação de um usuário de identidade. As *lojas* são classes de nível inferior que especificam como as entidades, como usuários e funções, são mantidas. Os armazenamentos seguem o padrão de repositório e estão intimamente acoplados ao mecanismo de persistência. Os gerentes são dissociados das lojas, o que significa que você pode substituir o mecanismo de persistência sem alterar o código do aplicativo (exceto para a configuração).

O diagrama a seguir mostra como um aplicativo Web interage com os gerentes, enquanto os armazenamentos interagem com a camada de acesso a dados.

![ASP.NET Core aplicativos funcionam com gerentes (por exemplo, "usermanager", "roleManager"). Os gerentes trabalham com lojas (por exemplo, "USERSTORE") que se comunicam com uma fonte de dados usando uma biblioteca como Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Para criar um provedor de armazenamento personalizado, crie a fonte de dados, a camada de acesso a dados e as classes de armazenamento que interagem com essa camada de acesso a dados (as caixas verde e cinza no diagrama acima). Você não precisa personalizar os gerentes ou o código do aplicativo que interage com eles (as caixas azuis acima).

Ao criar uma nova instância de `UserManager` ou `RoleManager` você fornece o tipo da classe de usuário e passa uma instância da classe Store como um argumento. Essa abordagem permite que você conecte suas classes personalizadas em ASP.NET Core. 

[Reconfigure o aplicativo para usar o novo provedor de armazenamento](#reconfigure-app-to-use-a-new-storage-provider) mostra como instanciar `UserManager` e `RoleManager` com um repositório personalizado.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core identidade armazena tipos de dados

[ASP.NET Core](https://github.com/aspnet/identity) tipos de dados de identidade são detalhados nas seções a seguir:

### <a name="users"></a>Usuários do

Usuários registrados do seu site da Web. O tipo [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) pode ser estendido ou usado como um exemplo para seu próprio tipo personalizado. Você não precisa herdar de um tipo específico para implementar sua própria solução de armazenamento de identidade personalizada.

### <a name="user-claims"></a>Declarações de usuário

Um conjunto de instruções (ou [declarações](/dotnet/api/system.security.claims.claim)) sobre o usuário que representa a identidade do usuário. Pode habilitar uma expressão maior da identidade do usuário do que pode ser obtido por meio de funções.

### <a name="user-logins"></a>Logons de usuário

Informações sobre o provedor de autenticação externa (como o Facebook ou um conta Microsoft) para usar ao fazer logon em um usuário. [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Funções do

Grupos de autorização para seu site. Inclui a ID da função e o nome da função (como "admin" ou "Employee"). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>A camada de acesso a dados

Este tópico pressupõe que você esteja familiarizado com o mecanismo de persistência que você pretende usar e como criar entidades para esse mecanismo. Este tópico não fornece detalhes sobre como criar repositórios ou classes de acesso a dados; Ele fornece algumas sugestões sobre decisões de design ao trabalhar com ASP.NET Core identidade.

Você tem muita liberdade ao criar a camada de acesso a dados para um provedor de armazenamento personalizado. Você só precisa criar mecanismos de persistência para os recursos que pretende usar em seu aplicativo. Por exemplo, se você não estiver usando funções em seu aplicativo, não precisará criar armazenamento para funções ou associações de função de usuário. Sua tecnologia e infraestrutura existente podem exigir uma estrutura muito diferente da implementação padrão da identidade de ASP.NET Core. Na camada de acesso a dados, você fornece a lógica para trabalhar com a estrutura da sua implementação de armazenamento.

A camada de acesso a dados fornece a lógica para salvar os dados de ASP.NET Core identidade em uma fonte de dados. A camada de acesso a dados para seu provedor de armazenamento personalizado pode incluir as seguintes classes para armazenar informações de usuário e função.

### <a name="context-class"></a>Classe de contexto

Encapsula as informações para se conectar ao mecanismo de persistência e executar consultas. Várias classes de dados exigem uma instância dessa classe, normalmente fornecidas por injeção de dependência. [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Armazenamento do usuário

Armazena e recupera informações do usuário (como o nome de usuário e o hash de senha). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Armazenamento de função

Armazena e recupera informações de função (como o nome da função). [Exemplo](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Armazenamento de userreivindicações

Armazena e recupera informações de declaração do usuário (como o tipo e o valor da declaração). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Armazenamento de userlogons

Armazena e recupera informações de logon de usuário (como um provedor de autenticação externa). [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Armazenamento de UserRole

Armazena e recupera quais funções são atribuídas a quais usuários. [Exemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**Dica:** Implemente apenas as classes que você pretende usar em seu aplicativo.

Nas classes de acesso a dados, forneça o código para executar operações de dados para seu mecanismo de persistência. Por exemplo, em um provedor personalizado, você pode ter o seguinte código para criar um novo usuário na classe *Store* :

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

A lógica de implementação para criar o usuário está no método `_usersTable.CreateAsync`, mostrado abaixo.

## <a name="customize-the-user-class"></a>Personalizar a classe de usuário

Ao implementar um provedor de armazenamento, crie uma classe de usuário que seja equivalente à [classe IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

No mínimo, sua classe de usuário deve incluir um `Id` e uma propriedade `UserName`.

A classe `IdentityUser` define as propriedades que o `UserManager` chama ao executar as operações solicitadas. O tipo padrão da propriedade `Id` é uma cadeia de caracteres, mas você pode herdar de `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` e especificar um tipo diferente. A estrutura espera que a implementação de armazenamento Manipule as conversões de tipo de dados.

## <a name="customize-the-user-store"></a>Personalizar o armazenamento do usuário

Crie uma classe `UserStore` que forneça os métodos para todas as operações de dados no usuário. Essa classe é equivalente à classe [USERSTORE&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) . Em sua classe de `UserStore`, implemente `IUserStore<TUser>` e as interfaces opcionais necessárias. Você seleciona quais interfaces opcionais implementar com base na funcionalidade fornecida em seu aplicativo.

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

As interfaces opcionais herdam de `IUserStore<TUser>`. Você pode ver um armazenamento de usuário de exemplo implementado parcialmente no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).

Dentro da classe `UserStore`, você usa as classes de acesso a dados que você criou para executar operações. Eles são passados usando a injeção de dependência. Por exemplo, no SQL Server com a implementação Dapper, a classe `UserStore` tem o método `CreateAsync` que usa uma instância de `DapperUsersTable` para inserir um novo registro:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Interfaces a serem implementadas ao personalizar o repositório de usuários

* **IUserStore**  
 A interface [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) é a única interface que você deve implementar no repositório de usuários. Ele define métodos para criar, atualizar, excluir e recuperar usuários.
* **IUserClaimStore**  
 A interface [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) define os métodos que você implementa para habilitar declarações de usuário. Ele contém métodos para adicionar, remover e recuperar declarações de usuário.
* **IUserLoginStore**  
 O [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) define os métodos que você implementa para habilitar provedores de autenticação externa. Ele contém métodos para adicionar, remover e recuperar logons de usuário e um método para recuperar um usuário com base nas informações de logon.
* **IUserRoleStore**  
 A interface [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) define os métodos que você implementa para mapear um usuário para uma função. Ele contém métodos para adicionar, remover e recuperar funções de um usuário e um método para verificar se um usuário está atribuído a uma função.
* **IUserPasswordStore**  
 A interface [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) define os métodos que você implementa para manter as senhas com hash. Ele contém métodos para obter e definir a senha de hash e um método que indica se o usuário definiu uma senha.
* **IUserSecurityStampStore**  
 A interface [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) define os métodos que você implementa para usar um carimbo de segurança para indicar se as informações da conta do usuário foram alteradas. Esse carimbo é atualizado quando um usuário altera a senha ou adiciona ou remove logons. Ele contém métodos para obter e definir o carimbo de segurança.
* **IUserTwoFactorStore**  
 A interface [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) define os métodos que você implementa para dar suporte à autenticação de dois fatores. Ele contém métodos para obter e definir se a autenticação de dois fatores está habilitada para um usuário.
* **IUserPhoneNumberStore**  
 A interface [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) define os métodos que você implementa para armazenar números de telefone de usuário. Ele contém métodos para obter e definir o número de telefone e se o número de telefone é confirmado.
* **IUserEmailStore**  
 A interface [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) define os métodos que você implementa para armazenar endereços de email do usuário. Ele contém métodos para obter e definir o endereço de email e se o email é confirmado.
* **IUserLockoutStore**  
 A interface [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) define os métodos que você implementa para armazenar informações sobre o bloqueio de uma conta. Ele contém métodos para controlar tentativas de acesso e bloqueios com falha.
* **IQueryableUserStore**  
 A interface [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) define os membros que você implementa para fornecer um armazenamento de usuário passível de consulta.

Você implementa apenas as interfaces que são necessárias em seu aplicativo. Por exemplo:

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

O namespace `Microsoft.AspNet.Identity.EntityFramework` contém implementações das classes [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)e [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) . Se você estiver usando esses recursos, talvez queira criar suas próprias versões dessas classes e definir as propriedades do seu aplicativo. No entanto, às vezes é mais eficiente não carregar essas entidades na memória ao executar operações básicas (como adicionar ou remover uma declaração de usuário). Em vez disso, as classes de armazenamento de back-end podem executar essas operações diretamente na fonte de dados. Por exemplo, o método `UserStore.GetClaimsAsync` pode chamar o método `userClaimTable.FindByUserId(user.Id)` para executar uma consulta diretamente na tabela e retornar uma lista de declarações.

## <a name="customize-the-role-class"></a>Personalizar a classe de função

Ao implementar um provedor de armazenamento de função, você pode criar um tipo de função personalizada. Ele não precisa implementar uma interface específica, mas deve ter uma `Id` e, normalmente, terá uma propriedade `Name`.

Veja a seguir um exemplo de classe de função:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Personalizar o repositório de funções

Você pode criar uma classe `RoleStore` que fornece os métodos para todas as operações de dados em funções. Essa classe é equivalente à classe [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) . Na classe `RoleStore`, você implementa a `IRoleStore<TRole>` e, opcionalmente, a interface `IQueryableRoleStore<TRole>`.

* **IRoleStore&lt;TRole&gt;**  
 A interface [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) define os métodos a serem implementados na classe de armazenamento de função. Ele contém métodos para criar, atualizar, excluir e recuperar funções.
* **RoleStore&lt;TRole&gt;**  
 Para personalizar `RoleStore`, crie uma classe que implemente a interface `IRoleStore<TRole>`. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Reconfigurar o aplicativo para usar um novo provedor de armazenamento

Depois de implementar um provedor de armazenamento, configure seu aplicativo para usá-lo. Se seu aplicativo usou o provedor padrão, substitua-o por seu provedor personalizado.

1. Remova o pacote NuGet `Microsoft.AspNetCore.EntityFramework.Identity`.
1. Se o provedor de armazenamento residir em um projeto ou pacote separado, adicione uma referência a ele.
1. Substitua todas as referências a `Microsoft.AspNetCore.EntityFramework.Identity` com uma instrução using para o namespace do seu provedor de armazenamento.
1. No método `ConfigureServices`, altere o método `AddIdentity` para usar seus tipos personalizados. Você pode criar seus próprios métodos de extensão para essa finalidade. Consulte [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) para obter um exemplo.
1. Se você estiver usando funções, atualize o `RoleManager` para usar sua classe `RoleStore`.
1. Atualize a cadeia de conexão e as credenciais para a configuração do seu aplicativo.

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

* [Provedores de armazenamento personalizados para a identidade ASP.NET 4. x](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core identidade](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash; este repositório inclui links para provedores de loja mantidos pela Comunidade.
