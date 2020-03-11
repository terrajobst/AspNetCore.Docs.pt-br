---
title: Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização
author: rick-anderson
description: Saiba como criar um aplicativo páginas Razor com dados protegidos por autorização do usuário. Inclui HTTPS, autenticação, segurança, identidade do ASP.NET Core.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 7710a8965771db02e601dafb7da752906bcd43e5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659575"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão ASP.NET Core MVC. A versão ASP.NET Core 1,1 deste tutorial está [nesta pasta.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) O exemplo de ASP.NET Core de 1,1 está nos [exemplos](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Este tutorial mostra como criar um aplicativo web do ASP.NET Core com dados de usuário protegidos por autorização. Ele exibe uma lista de contatos criada por usuários autenticados (registrados). Há três grupos de segurança:

* **Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.
* **Os gerentes** podem aprovar ou rejeitar dados de contato. Apenas os contatos aprovados são visíveis aos usuários.
* **Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.

As imagens neste documento não correspondem exatamente aos modelos mais recentes.

Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado. Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos. Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** . Outros usuários não verão o último registro até que um gerente ou administrador altere o status para "Aprovado".

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

A imagem a seguir mostra a tela de exibição de detalhes de um contato dos gerentes:

![Modo de exibição do Gerenciador de um contato](secure-data/_static/manager.png)

Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.

Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

O administrador tem todos os privilégios. Ele pode ler/editar/excluir todos os contatos e alterar os status deles.

O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

O exemplo contém os seguintes manipuladores de autorização:

* `ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.
* `ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.
* `ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Este tutorial é avançado. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticação](xref:security/authentication/identity)
* [Confirmação de conta e de recuperação de senha](xref:security/authentication/accconfirm)
* [Autorização](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>O aplicativo inicial e o concluído

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.

### <a name="the-starter-app"></a>O aplicativo inicial

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.

## <a name="secure-user-data"></a>Proteger os dados de usuário

As seções a seguir têm todas as principais etapas para criar um aplicativo com dados de usuários seguros. Talvez seja útil para fazer referência ao projeto concluído.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular os dados de contato ao usuário

Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários. Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) . O campo `Status` determina se um contato é visível por usuários gerais.

Criar uma nova migração e atualizar o banco de dados:

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

 Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`. Configurar a política de autenticação padrão para exigir que os usuários sejam autenticados protege recém-adicionado páginas do Razor e controladores. Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.

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

Adicione a ID de usuário do administrador e `ContactStatus` aos contatos. Torne um dos contatos "Enviado" e um "Rejeitado". Adicione a ID de usuário e o status para todos os contatos. Somente um contato é exibido:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Criar o proprietário, manager e manipuladores de autorização do administrador

Crie uma classe de `ContactIsOwnerAuthorizationHandler` na pasta *Authorization* . O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato. Manipuladores de autorização geralmente:

* Retorne `context.Succeed` quando os requisitos forem atendidos.
* Retorne `Task.CompletedTask` quando os requisitos não forem atendidos. `Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.

Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

O aplicativo permite que proprietários de contato possam editar/excluir/criar seus dados. `ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Criar um manipulador de autorização do Gerenciador

Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* . O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente. Somente gerentes possam aprovar ou rejeitar as alterações de conteúdo (novas ou alteradas).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Crie um manipulador de autorização do administrador

Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* . O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador. Administrador pode fazer todas as operações.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>O registro dos manipuladores de autorização

Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core. Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection). Adicione o seguinte código ao final do `ConfigureServices`:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons. Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.

## <a name="support-authorization"></a>Suporte à autorização

Nesta seção, você atualize as páginas Razor e adicione uma classe de requisitos de operações.

### <a name="review-the-contact-operations-requirements-class"></a>Examine a classe de requisitos de operações de contato

Examine a classe `ContactOperations`. Essa classe contém os requisitos de aplicativo dá suporte:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Criar uma classe base para as páginas do Razor de contatos

Crie uma classe base que contém os serviços usados em contatos páginas do Razor. A classe base coloca o código de inicialização em um único local:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

O código anterior:

* Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.
* Adiciona o serviço de `UserManager` de identidade.
* Adicione a `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Atualizar o CreateModel

Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Atualize o método `CreateModel.OnPostAsync` para:

* Adicione a ID de usuário ao modelo de `Contact`.
* Chame o manipulador de autorização para verificar se que o usuário tem permissão para criar contatos.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Atualizar o IndexModel

Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Atualizar o EditModel

Adicione um manipulador de autorização para verificar se que o usuário é proprietária do contato. Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente. O aplicativo não tiver acesso ao recurso quando atributos são avaliados. Autorização baseada em recursos deve ser imperativa. As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, carregá-los no modelo de página ou carregá-los dentro do manipulador em si. Com frequência, você acessa o recurso, passando a chave de recurso.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Atualizar o DeleteModel

Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se que o usuário tem permissão de exclusão no contato.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injetar o serviço de autorização em exibições

Atualmente, mostra a interface do usuário a edita e excluir links para os contatos, que o usuário não é possível modificar.

Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

A marcação anterior adiciona várias instruções `using`.

Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar links de usuários que não tem permissão para alterar os dados não proteger o aplicativo. Ocultar links torna o aplicativo mais fácil de usar, exibindo links só é válidas. Os usuários podem hack gerados URLs para invocar editar e excluir operações em dados que não possuem. A página do Razor ou controlador deve impor verificações de acesso para proteger os dados.

### <a name="update-details"></a>Detalhes da atualização

Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Atualize o modelo de página de detalhes:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Adicionar ou remover um usuário a uma função

Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:

* Remoção de privilégios de um usuário. Por exemplo, ativar mudo de um usuário em um aplicativo de chat.
* Adicionando privilégios a um usuário.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Diferenças entre desafio e proíba

Esse aplicativo define a política padrão para [exigir usuários autenticados](#require-authenticated-users). O código a seguir permite usuários anônimos. Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

No código anterior:

* Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado. Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.
* Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado. Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.

## <a name="test-the-completed-app"></a>Testar o aplicativo concluído

Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:

* Escolha uma senha forte: Use oito ou mais caracteres e pelo menos um caractere maiusculo, número e símbolo. Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.
* Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Se o aplicativo tem contatos:

* Exclua todos os registros na tabela `Contact`.
* Reinicie o aplicativo para propagar o banco de dados.

Uma maneira fácil de testar o aplicativo concluído é iniciar três diferentes navegadores (ou incógnita/InPrivate sessões). Em um navegador, registre um novo usuário (por exemplo, `test@example.com`). Entrar para cada navegador com um usuário diferente. Verifique se as seguintes operações:

* Usuários registrados podem exibir todos os dados de contato aprovados.
* Os usuários registrados podem editar/excluir seus próprios dados.
* Os gerentes podem Aprovar/rejeitar dados de contato. A exibição `Details` mostra os botões **aprovar** e **rejeitar** .
* Os administradores podem Aprovar/rejeitar e editar/excluir todos os dados.

| Usuário                | Propagada pelo aplicativo | {1&gt;Opções&lt;1}                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Não                | Editar/Excluir os próprios dados.                |
| manager@contoso.com | Sim               | Aprovar/rejeitar e editar/excluir os próprios dados. |
| admin@contoso.com   | Sim               | Aprovar/rejeitar e editar/excluir todos os dados. |

Crie um contato no navegador do administrador. Copie a URL para excluir e editar a partir do contato do administrador. Cole esses links no navegador do usuário de teste para verificar se que o usuário de teste não é possível executar essas operações.

## <a name="create-the-starter-app"></a>Criar o aplicativo inicial

* Criar um aplicativo páginas Razor denominado "ContactManager"
  * Crie o aplicativo com **contas de usuário individuais**.
  * O nome "ContactManager" para o namespace coincida com o namespace usado no exemplo.
  * `-uld` especifica o LocalDB em vez do SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Adicionar *modelos/Contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold o modelo de `Contact`.
* Criar a migração inicial e atualizar o banco de dados:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Se você tiver um bug com o comando `dotnet aspnet-codegenerator razorpage`, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Atualize a âncora **ContactManager** no arquivo *pages/Shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testar o aplicativo criando, editando e excluindo um contato

### <a name="seed-the-database"></a>Propagar o banco de dados

Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Chamar `SeedData.Initialize` de `Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Se o aplicativo propagado o banco de dados de teste. Se não houver nenhuma linha no banco de dados de contato, o método de propagação não será executado.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Este tutorial mostra como criar um aplicativo web do ASP.NET Core com dados de usuário protegidos por autorização. Ele exibe uma lista de contatos criada por usuários autenticados (registrados). Há três grupos de segurança:

* **Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.
* **Os gerentes** podem aprovar ou rejeitar dados de contato. Apenas os contatos aprovados são visíveis aos usuários.
* **Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.

Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado. Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos. Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** . Outros usuários não verão o último registro até que um gerente ou administrador altere o status para "Aprovado".

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

A imagem a seguir mostra a tela de exibição de detalhes de um contato dos gerentes:

![Modo de exibição do Gerenciador de um contato](secure-data/_static/manager.png)

Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.

Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

O administrador tem todos os privilégios. Ele pode ler/editar/excluir todos os contatos e alterar os status deles.

O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

O exemplo contém os seguintes manipuladores de autorização:

* `ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.
* `ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.
* `ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Este tutorial é avançado. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticação](xref:security/authentication/identity)
* [Confirmação de conta e de recuperação de senha](xref:security/authentication/accconfirm)
* [Autorização](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>O aplicativo inicial e o concluído

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.

### <a name="the-starter-app"></a>O aplicativo inicial

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.

## <a name="secure-user-data"></a>Proteger os dados de usuário

As seções a seguir têm todas as principais etapas para criar um aplicativo com dados de usuários seguros. Talvez seja útil para fazer referência ao projeto concluído.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular os dados de contato ao usuário

Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários. Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) . O campo `Status` determina se um contato é visível por usuários gerais.

Criar uma nova migração e atualizar o banco de dados:

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

 Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`. Configurar a política de autenticação padrão para exigir que os usuários sejam autenticados protege recém-adicionado páginas do Razor e controladores. Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.

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

Adicione a ID de usuário do administrador e `ContactStatus` aos contatos. Torne um dos contatos "Enviado" e um "Rejeitado". Adicione a ID de usuário e o status para todos os contatos. Somente um contato é exibido:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Criar o proprietário, manager e manipuladores de autorização do administrador

Crie uma pasta de *autorização* e crie uma classe de `ContactIsOwnerAuthorizationHandler` nela. O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato. Manipuladores de autorização geralmente:

* Retorne `context.Succeed` quando os requisitos forem atendidos.
* Retorne `Task.CompletedTask` quando os requisitos não forem atendidos. `Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.

Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

O aplicativo permite que proprietários de contato possam editar/excluir/criar seus dados. `ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Criar um manipulador de autorização do Gerenciador

Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* . O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente. Somente gerentes possam aprovar ou rejeitar as alterações de conteúdo (novas ou alteradas).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Crie um manipulador de autorização do administrador

Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* . O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador. Administrador pode fazer todas as operações.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>O registro dos manipuladores de autorização

Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core. Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection). Adicione o seguinte código ao final do `ConfigureServices`:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons. Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.

## <a name="support-authorization"></a>Suporte à autorização

Nesta seção, você atualize as páginas Razor e adicione uma classe de requisitos de operações.

### <a name="review-the-contact-operations-requirements-class"></a>Examine a classe de requisitos de operações de contato

Examine a classe `ContactOperations`. Essa classe contém os requisitos de aplicativo dá suporte:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Criar uma classe base para as páginas do Razor de contatos

Crie uma classe base que contém os serviços usados em contatos páginas do Razor. A classe base coloca o código de inicialização em um único local:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

O código anterior:

* Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.
* Adiciona o serviço de `UserManager` de identidade.
* Adicione a `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Atualizar o CreateModel

Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Atualize o método `CreateModel.OnPostAsync` para:

* Adicione a ID de usuário ao modelo de `Contact`.
* Chame o manipulador de autorização para verificar se que o usuário tem permissão para criar contatos.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Atualizar o IndexModel

Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Atualizar o EditModel

Adicione um manipulador de autorização para verificar se que o usuário é proprietária do contato. Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente. O aplicativo não tiver acesso ao recurso quando atributos são avaliados. Autorização baseada em recursos deve ser imperativa. As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, carregá-los no modelo de página ou carregá-los dentro do manipulador em si. Com frequência, você acessa o recurso, passando a chave de recurso.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Atualizar o DeleteModel

Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se que o usuário tem permissão de exclusão no contato.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injetar o serviço de autorização em exibições

Atualmente, mostra a interface do usuário a edita e excluir links para os contatos, que o usuário não é possível modificar.

Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

A marcação anterior adiciona várias instruções `using`.

Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar links de usuários que não tem permissão para alterar os dados não proteger o aplicativo. Ocultar links torna o aplicativo mais fácil de usar, exibindo links só é válidas. Os usuários podem hack gerados URLs para invocar editar e excluir operações em dados que não possuem. A página do Razor ou controlador deve impor verificações de acesso para proteger os dados.

### <a name="update-details"></a>Detalhes da atualização

Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Atualize o modelo de página de detalhes:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Adicionar ou remover um usuário a uma função

Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:

* Remoção de privilégios de um usuário. Por exemplo, ativar mudo de um usuário em um aplicativo de chat.
* Adicionando privilégios a um usuário.

## <a name="test-the-completed-app"></a>Testar o aplicativo concluído

Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:

* Escolha uma senha forte: Use oito ou mais caracteres e pelo menos um caractere maiusculo, número e símbolo. Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.
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

Uma maneira fácil de testar o aplicativo concluído é iniciar três diferentes navegadores (ou incógnita/InPrivate sessões). Em um navegador, registre um novo usuário (por exemplo, `test@example.com`). Entrar para cada navegador com um usuário diferente. Verifique se as seguintes operações:

* Usuários registrados podem exibir todos os dados de contato aprovados.
* Os usuários registrados podem editar/excluir seus próprios dados.
* Os gerentes podem Aprovar/rejeitar dados de contato. A exibição `Details` mostra os botões **aprovar** e **rejeitar** .
* Os administradores podem Aprovar/rejeitar e editar/excluir todos os dados.

| Usuário                | Propagada pelo aplicativo | {1&gt;Opções&lt;1}                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Não                | Editar/Excluir os próprios dados.                |
| manager@contoso.com | Sim               | Aprovar/rejeitar e editar/excluir os próprios dados. |
| admin@contoso.com   | Sim               | Aprovar/rejeitar e editar/excluir todos os dados. |

Crie um contato no navegador do administrador. Copie a URL para excluir e editar a partir do contato do administrador. Cole esses links no navegador do usuário de teste para verificar se que o usuário de teste não é possível executar essas operações.

## <a name="create-the-starter-app"></a>Criar o aplicativo inicial

* Criar um aplicativo páginas Razor denominado "ContactManager"
  * Crie o aplicativo com **contas de usuário individuais**.
  * O nome "ContactManager" para o namespace coincida com o namespace usado no exemplo.
  * `-uld` especifica o LocalDB em vez do SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Adicionar *modelos/Contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold o modelo de `Contact`.
* Criar a migração inicial e atualizar o banco de dados:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Atualize a âncora **ContactManager** no arquivo *pages/_Layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Testar o aplicativo criando, editando e excluindo um contato

### <a name="seed-the-database"></a>Propagar o banco de dados

Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .

Chamar `SeedData.Initialize` de `Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Se o aplicativo propagado o banco de dados de teste. Se não houver nenhuma linha no banco de dados de contato, o método de propagação não será executado.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Recursos adicionais

* [Criar um aplicativo Web .NET Core e do banco de dados SQL no serviço Azure App](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop). Este laboratório apresenta mais detalhes sobre os recursos de segurança introduzidos neste tutorial.
* <xref:security/authorization/introduction>
* [Autorização baseada em política personalizada](xref:security/authorization/policies)
