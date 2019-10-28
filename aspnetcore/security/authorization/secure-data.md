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
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="60dbe-104">Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="60dbe-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="60dbe-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="60dbe-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="60dbe-106">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="60dbe-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="60dbe-107">A versão ASP.NET Core 1,1 deste tutorial está [nesta pasta.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)</span><span class="sxs-lookup"><span data-stu-id="60dbe-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="60dbe-108">O exemplo de ASP.NET Core de 1,1 está nos [exemplos](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="60dbe-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="60dbe-109">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="60dbe-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="60dbe-110">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="60dbe-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="60dbe-111">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="60dbe-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="60dbe-112">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="60dbe-112">There are three security groups:</span></span>

* <span data-ttu-id="60dbe-113">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="60dbe-114">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="60dbe-115">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="60dbe-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="60dbe-116">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="60dbe-117">As imagens neste documento não correspondem exatamente aos modelos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="60dbe-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="60dbe-118">Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="60dbe-119">Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="60dbe-120">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="60dbe-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="60dbe-121">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="60dbe-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="60dbe-123">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="60dbe-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

<span data-ttu-id="60dbe-125">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="60dbe-125">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="60dbe-127">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="60dbe-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="60dbe-128">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="60dbe-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

<span data-ttu-id="60dbe-130">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="60dbe-130">The administrator has all privileges.</span></span> <span data-ttu-id="60dbe-131">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="60dbe-132">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="60dbe-133">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="60dbe-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="60dbe-134">`ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="60dbe-135">`ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="60dbe-136">`ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60dbe-137">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="60dbe-137">Prerequisites</span></span>

<span data-ttu-id="60dbe-138">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-138">This tutorial is advanced.</span></span> <span data-ttu-id="60dbe-139">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="60dbe-139">You should be familiar with:</span></span>

* [<span data-ttu-id="60dbe-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60dbe-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="60dbe-141">Autenticação</span><span class="sxs-lookup"><span data-stu-id="60dbe-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="60dbe-142">Confirmação de conta e de recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="60dbe-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="60dbe-143">Autorização</span><span class="sxs-lookup"><span data-stu-id="60dbe-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="60dbe-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="60dbe-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="60dbe-145">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="60dbe-145">The starter and completed app</span></span>

<span data-ttu-id="60dbe-146">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="60dbe-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="60dbe-147">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="60dbe-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="60dbe-148">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="60dbe-148">The starter app</span></span>

<span data-ttu-id="60dbe-149">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="60dbe-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="60dbe-150">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="60dbe-151">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="60dbe-151">Secure user data</span></span>

<span data-ttu-id="60dbe-152">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="60dbe-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="60dbe-153">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="60dbe-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="60dbe-154">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="60dbe-154">Tie the contact data to the user</span></span>

<span data-ttu-id="60dbe-155">Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="60dbe-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="60dbe-156">Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="60dbe-157">`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="60dbe-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="60dbe-158">O campo `Status` determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="60dbe-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="60dbe-159">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="60dbe-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="60dbe-160">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="60dbe-160">Add Role services to Identity</span></span>

<span data-ttu-id="60dbe-161">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="60dbe-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="60dbe-162">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="60dbe-162">Require authenticated users</span></span>

<span data-ttu-id="60dbe-163">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="60dbe-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="60dbe-164">Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="60dbe-165">Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor Pages e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="60dbe-166">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="60dbe-167">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice e privacidade para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="60dbe-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="60dbe-168">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="60dbe-168">Configure the test account</span></span>

<span data-ttu-id="60dbe-169">A classe `SeedData` cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="60dbe-170">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="60dbe-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="60dbe-171">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="60dbe-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="60dbe-172">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="60dbe-173">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="60dbe-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="60dbe-174">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="60dbe-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="60dbe-175">Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="60dbe-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="60dbe-176">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="60dbe-177">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="60dbe-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="60dbe-178">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="60dbe-179">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="60dbe-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="60dbe-180">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="60dbe-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="60dbe-181">Crie uma classe de `ContactIsOwnerAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="60dbe-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="60dbe-182">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="60dbe-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="60dbe-183">O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="60dbe-184">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="60dbe-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="60dbe-185">Retorne `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="60dbe-186">Retorne `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="60dbe-187">`Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="60dbe-188">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="60dbe-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="60dbe-189">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="60dbe-190">`ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="60dbe-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="60dbe-191">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="60dbe-191">Create a manager authorization handler</span></span>

<span data-ttu-id="60dbe-192">Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="60dbe-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="60dbe-193">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="60dbe-194">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="60dbe-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="60dbe-195">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="60dbe-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="60dbe-196">Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="60dbe-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="60dbe-197">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="60dbe-198">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="60dbe-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="60dbe-199">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="60dbe-199">Register the authorization handlers</span></span>

<span data-ttu-id="60dbe-200">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="60dbe-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="60dbe-201">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="60dbe-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="60dbe-202">Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="60dbe-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="60dbe-203">Adicione o seguinte código ao final do `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="60dbe-204">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="60dbe-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="60dbe-205">Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="60dbe-206">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="60dbe-206">Support authorization</span></span>

<span data-ttu-id="60dbe-207">Nesta seção, você atualiza o Razor Pages e adiciona uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="60dbe-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="60dbe-208">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="60dbe-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="60dbe-209">Examine a classe `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="60dbe-210">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="60dbe-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="60dbe-211">Criar uma classe base para os contatos Razor Pages</span><span class="sxs-lookup"><span data-stu-id="60dbe-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="60dbe-212">Crie uma classe base que contenha os serviços usados na Razor Pages de contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="60dbe-213">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="60dbe-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="60dbe-214">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="60dbe-214">The preceding code:</span></span>

* <span data-ttu-id="60dbe-215">Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="60dbe-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="60dbe-216">Adiciona o serviço de `UserManager` de identidade.</span><span class="sxs-lookup"><span data-stu-id="60dbe-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="60dbe-217">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="60dbe-218">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="60dbe-218">Update the CreateModel</span></span>

<span data-ttu-id="60dbe-219">Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="60dbe-220">Atualize o método `CreateModel.OnPostAsync` para:</span><span class="sxs-lookup"><span data-stu-id="60dbe-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="60dbe-221">Adicione a ID de usuário ao modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="60dbe-222">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="60dbe-223">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="60dbe-223">Update the IndexModel</span></span>

<span data-ttu-id="60dbe-224">Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="60dbe-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="60dbe-225">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="60dbe-225">Update the EditModel</span></span>

<span data-ttu-id="60dbe-226">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="60dbe-227">Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="60dbe-228">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="60dbe-229">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="60dbe-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="60dbe-230">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="60dbe-231">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="60dbe-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="60dbe-232">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="60dbe-232">Update the DeleteModel</span></span>

<span data-ttu-id="60dbe-233">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="60dbe-234">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="60dbe-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="60dbe-235">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="60dbe-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="60dbe-236">Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="60dbe-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="60dbe-237">A marcação anterior adiciona várias instruções `using`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="60dbe-238">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="60dbe-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="60dbe-239">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="60dbe-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="60dbe-240">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="60dbe-241">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="60dbe-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="60dbe-242">A página Razor ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="60dbe-243">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="60dbe-243">Update Details</span></span>

<span data-ttu-id="60dbe-244">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="60dbe-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="60dbe-245">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="60dbe-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="60dbe-246">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="60dbe-246">Add or remove a user to a role</span></span>

<span data-ttu-id="60dbe-247">Consulte [este problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="60dbe-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="60dbe-248">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="60dbe-248">Removing privileges from a user.</span></span> <span data-ttu-id="60dbe-249">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="60dbe-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="60dbe-250">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="60dbe-250">Adding privileges to a user.</span></span>

## <a name="differences-between-challenge-vs-forbid"></a><span data-ttu-id="60dbe-251">Diferenças entre desafio versus proíba</span><span class="sxs-lookup"><span data-stu-id="60dbe-251">Differences between Challenge vs Forbid</span></span>

<span data-ttu-id="60dbe-252">Esse aplicativo define a política padrão para [exigir usuários autenticados](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="60dbe-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="60dbe-253">O código a seguir permite usuários anônimos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-253">The following code allows anonymous users.</span></span> <span data-ttu-id="60dbe-254">Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.</span><span class="sxs-lookup"><span data-stu-id="60dbe-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="60dbe-255">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="60dbe-255">In the preceding code:</span></span>

* <span data-ttu-id="60dbe-256">Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="60dbe-257">Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.</span><span class="sxs-lookup"><span data-stu-id="60dbe-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="60dbe-258">Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="60dbe-259">Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="60dbe-260">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="60dbe-260">Test the completed app</span></span>

<span data-ttu-id="60dbe-261">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="60dbe-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="60dbe-262">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="60dbe-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="60dbe-263">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="60dbe-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="60dbe-264">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="60dbe-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="60dbe-265">Se o aplicativo tiver contatos:</span><span class="sxs-lookup"><span data-stu-id="60dbe-265">If the app has contacts:</span></span>

* <span data-ttu-id="60dbe-266">Exclua todos os registros na tabela `Contact`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="60dbe-267">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="60dbe-268">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="60dbe-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="60dbe-269">Em um navegador, registre um novo usuário (por exemplo, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="60dbe-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="60dbe-270">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="60dbe-271">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="60dbe-271">Verify the following operations:</span></span>

* <span data-ttu-id="60dbe-272">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="60dbe-273">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="60dbe-274">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="60dbe-275">A exibição `Details` mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="60dbe-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="60dbe-276">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="60dbe-277">User</span><span class="sxs-lookup"><span data-stu-id="60dbe-277">User</span></span>                | <span data-ttu-id="60dbe-278">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="60dbe-278">Seeded by the app</span></span> | <span data-ttu-id="60dbe-279">Opções</span><span class="sxs-lookup"><span data-stu-id="60dbe-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="60dbe-280">Não</span><span class="sxs-lookup"><span data-stu-id="60dbe-280">No</span></span>                | <span data-ttu-id="60dbe-281">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="60dbe-282">Sim</span><span class="sxs-lookup"><span data-stu-id="60dbe-282">Yes</span></span>               | <span data-ttu-id="60dbe-283">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="60dbe-284">Sim</span><span class="sxs-lookup"><span data-stu-id="60dbe-284">Yes</span></span>               | <span data-ttu-id="60dbe-285">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="60dbe-286">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="60dbe-287">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="60dbe-288">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="60dbe-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="60dbe-289">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="60dbe-289">Create the starter app</span></span>

* <span data-ttu-id="60dbe-290">Criar um aplicativo Razor Pages chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="60dbe-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="60dbe-291">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="60dbe-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="60dbe-292">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="60dbe-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="60dbe-293">`-uld` especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="60dbe-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="60dbe-294">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="60dbe-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="60dbe-295">Scaffold o modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="60dbe-296">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="60dbe-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="60dbe-297">Se você tiver um bug com o comando `dotnet aspnet-codegenerator razorpage`, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="60dbe-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="60dbe-298">Atualize a âncora **ContactManager** no arquivo *pages/Shared/_ layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="60dbe-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="60dbe-299">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="60dbe-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="60dbe-300">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="60dbe-300">Seed the database</span></span>

<span data-ttu-id="60dbe-301">Adicione a classe [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :</span><span class="sxs-lookup"><span data-stu-id="60dbe-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="60dbe-302">Chamar `SeedData.Initialize` de `Main`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="60dbe-303">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-303">Test that the app seeded the database.</span></span> <span data-ttu-id="60dbe-304">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="60dbe-305">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="60dbe-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="60dbe-306">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="60dbe-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="60dbe-307">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="60dbe-307">There are three security groups:</span></span>

* <span data-ttu-id="60dbe-308">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="60dbe-309">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="60dbe-310">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="60dbe-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="60dbe-311">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="60dbe-312">Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="60dbe-313">Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="60dbe-314">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="60dbe-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="60dbe-315">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="60dbe-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="60dbe-317">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="60dbe-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

<span data-ttu-id="60dbe-319">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="60dbe-319">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="60dbe-321">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="60dbe-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="60dbe-322">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="60dbe-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

<span data-ttu-id="60dbe-324">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="60dbe-324">The administrator has all privileges.</span></span> <span data-ttu-id="60dbe-325">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="60dbe-326">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="60dbe-327">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="60dbe-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="60dbe-328">`ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="60dbe-329">`ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="60dbe-330">`ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60dbe-331">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="60dbe-331">Prerequisites</span></span>

<span data-ttu-id="60dbe-332">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-332">This tutorial is advanced.</span></span> <span data-ttu-id="60dbe-333">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="60dbe-333">You should be familiar with:</span></span>

* [<span data-ttu-id="60dbe-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60dbe-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="60dbe-335">Autenticação</span><span class="sxs-lookup"><span data-stu-id="60dbe-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="60dbe-336">Confirmação de conta e de recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="60dbe-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="60dbe-337">Autorização</span><span class="sxs-lookup"><span data-stu-id="60dbe-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="60dbe-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="60dbe-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="60dbe-339">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="60dbe-339">The starter and completed app</span></span>

<span data-ttu-id="60dbe-340">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="60dbe-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="60dbe-341">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="60dbe-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="60dbe-342">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="60dbe-342">The starter app</span></span>

<span data-ttu-id="60dbe-343">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="60dbe-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="60dbe-344">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="60dbe-345">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="60dbe-345">Secure user data</span></span>

<span data-ttu-id="60dbe-346">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="60dbe-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="60dbe-347">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="60dbe-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="60dbe-348">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="60dbe-348">Tie the contact data to the user</span></span>

<span data-ttu-id="60dbe-349">Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="60dbe-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="60dbe-350">Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="60dbe-351">`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="60dbe-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="60dbe-352">O campo `Status` determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="60dbe-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="60dbe-353">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="60dbe-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="60dbe-354">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="60dbe-354">Add Role services to Identity</span></span>

<span data-ttu-id="60dbe-355">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="60dbe-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="60dbe-356">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="60dbe-356">Require authenticated users</span></span>

<span data-ttu-id="60dbe-357">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="60dbe-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="60dbe-358">Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="60dbe-359">Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor Pages e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="60dbe-360">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="60dbe-361">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="60dbe-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="60dbe-362">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="60dbe-362">Configure the test account</span></span>

<span data-ttu-id="60dbe-363">A classe `SeedData` cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="60dbe-364">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="60dbe-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="60dbe-365">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="60dbe-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="60dbe-366">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="60dbe-367">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="60dbe-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="60dbe-368">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="60dbe-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="60dbe-369">Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="60dbe-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="60dbe-370">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="60dbe-371">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="60dbe-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="60dbe-372">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="60dbe-373">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="60dbe-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="60dbe-374">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="60dbe-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="60dbe-375">Crie uma pasta de *autorização* e crie uma classe de `ContactIsOwnerAuthorizationHandler` nela.</span><span class="sxs-lookup"><span data-stu-id="60dbe-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="60dbe-376">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="60dbe-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="60dbe-377">O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="60dbe-378">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="60dbe-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="60dbe-379">Retorne `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="60dbe-380">Retorne `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="60dbe-381">`Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="60dbe-382">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="60dbe-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="60dbe-383">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="60dbe-384">`ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="60dbe-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="60dbe-385">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="60dbe-385">Create a manager authorization handler</span></span>

<span data-ttu-id="60dbe-386">Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="60dbe-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="60dbe-387">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="60dbe-388">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="60dbe-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="60dbe-389">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="60dbe-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="60dbe-390">Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="60dbe-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="60dbe-391">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="60dbe-392">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="60dbe-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="60dbe-393">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="60dbe-393">Register the authorization handlers</span></span>

<span data-ttu-id="60dbe-394">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="60dbe-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="60dbe-395">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="60dbe-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="60dbe-396">Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="60dbe-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="60dbe-397">Adicione o seguinte código ao final do `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="60dbe-398">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="60dbe-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="60dbe-399">Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="60dbe-400">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="60dbe-400">Support authorization</span></span>

<span data-ttu-id="60dbe-401">Nesta seção, você atualiza o Razor Pages e adiciona uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="60dbe-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="60dbe-402">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="60dbe-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="60dbe-403">Examine a classe `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="60dbe-404">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="60dbe-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="60dbe-405">Criar uma classe base para os contatos Razor Pages</span><span class="sxs-lookup"><span data-stu-id="60dbe-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="60dbe-406">Crie uma classe base que contenha os serviços usados na Razor Pages de contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="60dbe-407">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="60dbe-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="60dbe-408">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="60dbe-408">The preceding code:</span></span>

* <span data-ttu-id="60dbe-409">Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="60dbe-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="60dbe-410">Adiciona o serviço de `UserManager` de identidade.</span><span class="sxs-lookup"><span data-stu-id="60dbe-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="60dbe-411">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="60dbe-412">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="60dbe-412">Update the CreateModel</span></span>

<span data-ttu-id="60dbe-413">Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="60dbe-414">Atualize o método `CreateModel.OnPostAsync` para:</span><span class="sxs-lookup"><span data-stu-id="60dbe-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="60dbe-415">Adicione a ID de usuário ao modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="60dbe-416">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="60dbe-417">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="60dbe-417">Update the IndexModel</span></span>

<span data-ttu-id="60dbe-418">Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="60dbe-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="60dbe-419">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="60dbe-419">Update the EditModel</span></span>

<span data-ttu-id="60dbe-420">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="60dbe-421">Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="60dbe-422">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="60dbe-423">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="60dbe-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="60dbe-424">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="60dbe-425">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="60dbe-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="60dbe-426">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="60dbe-426">Update the DeleteModel</span></span>

<span data-ttu-id="60dbe-427">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="60dbe-428">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="60dbe-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="60dbe-429">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="60dbe-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="60dbe-430">Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="60dbe-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="60dbe-431">A marcação anterior adiciona várias instruções `using`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="60dbe-432">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="60dbe-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="60dbe-433">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="60dbe-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="60dbe-434">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="60dbe-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="60dbe-435">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="60dbe-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="60dbe-436">A página Razor ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="60dbe-437">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="60dbe-437">Update Details</span></span>

<span data-ttu-id="60dbe-438">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="60dbe-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="60dbe-439">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="60dbe-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="60dbe-440">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="60dbe-440">Add or remove a user to a role</span></span>

<span data-ttu-id="60dbe-441">Consulte [este problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="60dbe-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="60dbe-442">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="60dbe-442">Removing privileges from a user.</span></span> <span data-ttu-id="60dbe-443">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="60dbe-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="60dbe-444">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="60dbe-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="60dbe-445">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="60dbe-445">Test the completed app</span></span>

<span data-ttu-id="60dbe-446">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="60dbe-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="60dbe-447">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="60dbe-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="60dbe-448">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="60dbe-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="60dbe-449">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="60dbe-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="60dbe-450">Remover e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="60dbe-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="60dbe-451">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="60dbe-452">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="60dbe-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="60dbe-453">Em um navegador, registre um novo usuário (por exemplo, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="60dbe-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="60dbe-454">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="60dbe-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="60dbe-455">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="60dbe-455">Verify the following operations:</span></span>

* <span data-ttu-id="60dbe-456">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="60dbe-457">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="60dbe-458">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="60dbe-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="60dbe-459">A exibição `Details` mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="60dbe-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="60dbe-460">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="60dbe-461">User</span><span class="sxs-lookup"><span data-stu-id="60dbe-461">User</span></span>                | <span data-ttu-id="60dbe-462">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="60dbe-462">Seeded by the app</span></span> | <span data-ttu-id="60dbe-463">Opções</span><span class="sxs-lookup"><span data-stu-id="60dbe-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="60dbe-464">Não</span><span class="sxs-lookup"><span data-stu-id="60dbe-464">No</span></span>                | <span data-ttu-id="60dbe-465">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="60dbe-466">Sim</span><span class="sxs-lookup"><span data-stu-id="60dbe-466">Yes</span></span>               | <span data-ttu-id="60dbe-467">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="60dbe-468">Sim</span><span class="sxs-lookup"><span data-stu-id="60dbe-468">Yes</span></span>               | <span data-ttu-id="60dbe-469">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="60dbe-470">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="60dbe-471">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="60dbe-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="60dbe-472">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="60dbe-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="60dbe-473">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="60dbe-473">Create the starter app</span></span>

* <span data-ttu-id="60dbe-474">Criar um aplicativo Razor Pages chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="60dbe-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="60dbe-475">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="60dbe-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="60dbe-476">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="60dbe-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="60dbe-477">`-uld` especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="60dbe-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="60dbe-478">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="60dbe-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="60dbe-479">Scaffold o modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="60dbe-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="60dbe-480">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="60dbe-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="60dbe-481">Atualize a âncora **ContactManager** no arquivo *pages/_ layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="60dbe-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="60dbe-482">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="60dbe-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="60dbe-483">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="60dbe-483">Seed the database</span></span>

<span data-ttu-id="60dbe-484">Adicione a classe [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .</span><span class="sxs-lookup"><span data-stu-id="60dbe-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="60dbe-485">Chamar `SeedData.Initialize` de `Main`:</span><span class="sxs-lookup"><span data-stu-id="60dbe-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="60dbe-486">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="60dbe-486">Test that the app seeded the database.</span></span> <span data-ttu-id="60dbe-487">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="60dbe-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="60dbe-488">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="60dbe-488">Additional resources</span></span>

* [<span data-ttu-id="60dbe-489">Criar um aplicativo Web .NET Core e do banco de dados SQL no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="60dbe-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="60dbe-490">[ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="60dbe-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="60dbe-491">Este laboratório apresenta mais detalhes sobre os recursos de segurança apresentados neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="60dbe-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="60dbe-492">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="60dbe-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
