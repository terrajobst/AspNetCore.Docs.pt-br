---
title: Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização
author: rick-anderson
description: Saiba como criar um aplicativo Razor Pages com dados de usuário protegidos por autorização. Inclui HTTPS, autenticação, segurança ASP.NET Core identidade.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 6e2f785a6dc014884f105766686f284cb2685530
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816156"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão ASP.NET Core MVC. A versão ASP.NET Core 1,1 deste tutorial está [nesta pasta.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) O exemplo de ASP.NET Core de 1,1 está nos [exemplos](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram. Há três grupos de segurança:

* **Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.
* **Os gerentes** podem aprovar ou rejeitar dados de contato. Somente contatos aprovados são visíveis para os usuários.
* **Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.

As imagens neste documento não correspondem exatamente aos modelos mais recentes.

Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado. Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos. Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** . Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:

![Exibição do gerente de um contato](secure-data/_static/manager.png)

Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.

Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

O administrador tem todos os privilégios. Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.

O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

O exemplo contém os seguintes manipuladores de autorização:

* `ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.
* `ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.
* `ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.

## <a name="prerequisites"></a>Prerequisites

Este tutorial é avançado. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticação](xref:security/authentication/identity)
* [Confirmação de conta e de recuperação de senha](xref:security/authentication/accconfirm)
* [Autorização](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>O aplicativo iniciador e concluído

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.

### <a name="the-starter-app"></a>O aplicativo inicial

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.

## <a name="secure-user-data"></a>Proteger dados do usuário

As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro. Talvez você ache útil fazer referência ao projeto concluído.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular os dados de contato ao usuário

Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários. Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) . O campo `Status` determina se um contato é visível por usuários gerais.

Crie uma nova migração e atualize o banco de dados:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Adicionar serviços de função à identidade

Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>Exigir usuários autenticados

Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`. Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor Pages e controladores adicionados recentemente. Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.

Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice e privacidade para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Configurar a conta de teste

A classe `SeedData` cria duas contas: administrador e Gerenciador. Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas. Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.

Atualize `Main` para usar a senha de teste:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Criar as contas de teste e atualizar os contatos

Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Adicione a ID de usuário do administrador e `ContactStatus` aos contatos. Faça um dos contatos "enviado" e um "rejeitado". Adicione a ID de usuário e o status a todos os contatos. Apenas um contato é mostrado:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Criar manipuladores de autorização de administrador, gerente e proprietário

Crie uma classe de `ContactIsOwnerAuthorizationHandler` na pasta *Authorization* . O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato. Manipuladores de autorização geralmente:

* Retorne `context.Succeed` quando os requisitos forem atendidos.
* Retorne `Task.CompletedTask` quando os requisitos não forem atendidos. `Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.

Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados. `ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Criar um manipulador de autorização de gerente

Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* . O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente. Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Criar um manipulador de autorização de administrador

Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* . O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador. O administrador pode realizar todas as operações.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrar os manipuladores de autorização

Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core. Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection). Adicione o seguinte código ao final do `ConfigureServices`:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons. Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.

## <a name="support-authorization"></a>Autorização de suporte

Nesta seção, você atualiza o Razor Pages e adiciona uma classe de requisitos de operações.

### <a name="review-the-contact-operations-requirements-class"></a>Examinar a classe de requisitos de operações de contato

Examine a classe `ContactOperations`. Essa classe contém os requisitos aos quais o aplicativo dá suporte:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Criar uma classe base para os contatos Razor Pages

Crie uma classe base que contenha os serviços usados na Razor Pages de contatos. A classe base coloca o código de inicialização em um local:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

O código anterior:

* Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.
* Adiciona o serviço de `UserManager` de identidade.
* Adicione a `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Atualizar o CREATEMODEL

Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Atualize o método `CreateModel.OnPostAsync` para:

* Adicione a ID de usuário ao modelo de `Contact`.
* Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Atualizar o IndexModel

Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Atualizar o EditModel

Adicione um manipulador de autorização para verificar se o usuário possui o contato. Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente. O aplicativo não tem acesso ao recurso quando os atributos são avaliados. A autorização baseada em recursos deve ser imperativa. As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador. Você acessa com frequência o recurso passando a chave de recurso.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Atualizar o DeleteModel

Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injetar o serviço de autorização nas exibições

Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.

Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

A marcação anterior adiciona várias instruções `using`.

Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo. Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos. Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem. A página Razor ou o controlador deve impor verificações de acesso para proteger os dados.

### <a name="update-details"></a>Atualizar detalhes

Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Atualize o modelo de página de detalhes:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Adicionar ou remover um usuário de uma função

Consulte [este problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:

* Removendo privilégios de um usuário. Por exemplo, ativar mudo de um usuário em um aplicativo de chat.
* Adicionando privilégios a um usuário.

## <a name="differences-between-challenge-vs-forbid"></a>Diferenças entre desafio versus proíba

Esse aplicativo define a política padrão para [exigir usuários autenticados](#require-authenticated-users). O código a seguir permite usuários anônimos. Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

No código anterior:

* Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado. Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.
* Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado. Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.

## <a name="test-the-completed-app"></a>Testar o aplicativo concluído

Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:

* Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas. Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.
* Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Se o aplicativo tiver contatos:

* Exclua todos os registros na tabela `Contact`.
* Reinicie o aplicativo para propagar o banco de dados.

Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate). Em um navegador, registre um novo usuário (por exemplo, `test@example.com`). Entre em cada navegador com um usuário diferente. Verifique as seguintes operações:

* Os usuários registrados podem exibir todos os dados de contato aprovados.
* Os usuários registrados podem editar/excluir seus próprios dados.
* Os gerentes podem aprovar/rejeitar dados de contato. A exibição `Details` mostra os botões **aprovar** e **rejeitar** .
* Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.

| User                | Propagado pelo aplicativo | Opções                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Não                | Edite/exclua os próprios dados.                |
| manager@contoso.com | Sim               | Aprovar/rejeitar e editar/excluir os próprios dados. |
| admin@contoso.com   | Sim               | Aprovar/rejeitar e editar/excluir todos os dados. |

Crie um contato no navegador do administrador. Copie a URL para excluir e editar do contato do administrador. Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.

## <a name="create-the-starter-app"></a>Criar o aplicativo inicial

* Criar um aplicativo Razor Pages chamado "ContactManager"
  * Crie o aplicativo com **contas de usuário individuais**.
  * Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.
  * `-uld` especifica o LocalDB em vez do SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Adicionar *modelos/Contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold o modelo de `Contact`.
* Crie uma migração inicial e atualize o banco de dados:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Se você tiver um bug com o comando `dotnet aspnet-codegenerator razorpage`, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Atualize a âncora **ContactManager** no arquivo *pages/Shared/_ layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testar o aplicativo Criando, editando e excluindo um contato

### <a name="seed-the-database"></a>Propagar o banco de dados

Adicione a classe [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Chamar `SeedData.Initialize` de `Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Teste se o aplicativo propagau o banco de dados. Se houver linhas no BD de contato, o método de semente não será executado.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram. Há três grupos de segurança:

* **Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.
* **Os gerentes** podem aprovar ou rejeitar dados de contato. Somente contatos aprovados são visíveis para os usuários.
* **Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.

Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado. Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos. Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** . Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:

![Exibição do gerente de um contato](secure-data/_static/manager.png)

Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.

Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

O administrador tem todos os privilégios. Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.

O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

O exemplo contém os seguintes manipuladores de autorização:

* `ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.
* `ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.
* `ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.

## <a name="prerequisites"></a>Prerequisites

Este tutorial é avançado. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticação](xref:security/authentication/identity)
* [Confirmação de conta e de recuperação de senha](xref:security/authentication/accconfirm)
* [Autorização](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>O aplicativo iniciador e concluído

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.

### <a name="the-starter-app"></a>O aplicativo inicial

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.

## <a name="secure-user-data"></a>Proteger dados do usuário

As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro. Talvez você ache útil fazer referência ao projeto concluído.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular os dados de contato ao usuário

Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários. Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) . O campo `Status` determina se um contato é visível por usuários gerais.

Crie uma nova migração e atualize o banco de dados:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Adicionar serviços de função à identidade

Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>Exigir usuários autenticados

Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`. Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor Pages e controladores adicionados recentemente. Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.

Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Configurar a conta de teste

A classe `SeedData` cria duas contas: administrador e Gerenciador. Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas. Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.

Atualize `Main` para usar a senha de teste:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Criar as contas de teste e atualizar os contatos

Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Adicione a ID de usuário do administrador e `ContactStatus` aos contatos. Faça um dos contatos "enviado" e um "rejeitado". Adicione a ID de usuário e o status a todos os contatos. Apenas um contato é mostrado:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Criar manipuladores de autorização de administrador, gerente e proprietário

Crie uma pasta de *autorização* e crie uma classe de `ContactIsOwnerAuthorizationHandler` nela. O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato. Manipuladores de autorização geralmente:

* Retorne `context.Succeed` quando os requisitos forem atendidos.
* Retorne `Task.CompletedTask` quando os requisitos não forem atendidos. `Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.

Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados. `ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Criar um manipulador de autorização de gerente

Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* . O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente. Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Criar um manipulador de autorização de administrador

Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* . O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador. O administrador pode realizar todas as operações.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrar os manipuladores de autorização

Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core. Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection). Adicione o seguinte código ao final do `ConfigureServices`:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons. Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.

## <a name="support-authorization"></a>Autorização de suporte

Nesta seção, você atualiza o Razor Pages e adiciona uma classe de requisitos de operações.

### <a name="review-the-contact-operations-requirements-class"></a>Examinar a classe de requisitos de operações de contato

Examine a classe `ContactOperations`. Essa classe contém os requisitos aos quais o aplicativo dá suporte:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Criar uma classe base para os contatos Razor Pages

Crie uma classe base que contenha os serviços usados na Razor Pages de contatos. A classe base coloca o código de inicialização em um local:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

O código anterior:

* Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.
* Adiciona o serviço de `UserManager` de identidade.
* Adicione a `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Atualizar o CREATEMODEL

Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Atualize o método `CreateModel.OnPostAsync` para:

* Adicione a ID de usuário ao modelo de `Contact`.
* Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Atualizar o IndexModel

Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Atualizar o EditModel

Adicione um manipulador de autorização para verificar se o usuário possui o contato. Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente. O aplicativo não tem acesso ao recurso quando os atributos são avaliados. A autorização baseada em recursos deve ser imperativa. As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador. Você acessa com frequência o recurso passando a chave de recurso.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Atualizar o DeleteModel

Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injetar o serviço de autorização nas exibições

Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.

Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

A marcação anterior adiciona várias instruções `using`.

Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo. Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos. Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem. A página Razor ou o controlador deve impor verificações de acesso para proteger os dados.

### <a name="update-details"></a>Atualizar detalhes

Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Atualize o modelo de página de detalhes:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Adicionar ou remover um usuário de uma função

Consulte [este problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:

* Removendo privilégios de um usuário. Por exemplo, ativar mudo de um usuário em um aplicativo de chat.
* Adicionando privilégios a um usuário.

## <a name="test-the-completed-app"></a>Testar o aplicativo concluído

Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:

* Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas. Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.
* Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Remover e atualizar o banco de dados

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Reinicie o aplicativo para propagar o banco de dados.

Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate). Em um navegador, registre um novo usuário (por exemplo, `test@example.com`). Entre em cada navegador com um usuário diferente. Verifique as seguintes operações:

* Os usuários registrados podem exibir todos os dados de contato aprovados.
* Os usuários registrados podem editar/excluir seus próprios dados.
* Os gerentes podem aprovar/rejeitar dados de contato. A exibição `Details` mostra os botões **aprovar** e **rejeitar** .
* Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.

| User                | Propagado pelo aplicativo | Opções                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Não                | Edite/exclua os próprios dados.                |
| manager@contoso.com | Sim               | Aprovar/rejeitar e editar/excluir os próprios dados. |
| admin@contoso.com   | Sim               | Aprovar/rejeitar e editar/excluir todos os dados. |

Crie um contato no navegador do administrador. Copie a URL para excluir e editar do contato do administrador. Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.

## <a name="create-the-starter-app"></a>Criar o aplicativo inicial

* Criar um aplicativo Razor Pages chamado "ContactManager"
  * Crie o aplicativo com **contas de usuário individuais**.
  * Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.
  * `-uld` especifica o LocalDB em vez do SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Adicionar *modelos/Contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold o modelo de `Contact`.
* Crie uma migração inicial e atualize o banco de dados:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Atualize a âncora **ContactManager** no arquivo *pages/_ layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Testar o aplicativo Criando, editando e excluindo um contato

### <a name="seed-the-database"></a>Propagar o banco de dados

Adicione a classe [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .

Chamar `SeedData.Initialize` de `Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Teste se o aplicativo propagau o banco de dados. Se houver linhas no BD de contato, o método de semente não será executado.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Recursos adicionais

* [Criar um aplicativo Web .NET Core e do banco de dados SQL no serviço Azure App](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop). Este laboratório apresenta mais detalhes sobre os recursos de segurança apresentados neste tutorial.
* <xref:security/authorization/introduction>
* [Autorização baseada em política personalizada](xref:security/authorization/policies)
