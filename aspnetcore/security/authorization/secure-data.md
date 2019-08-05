---
title: Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização
author: rick-anderson
description: Saiba como criar um aplicativo páginas Razor com dados protegidos por autorização do usuário. Inclui HTTPS, autenticação, segurança, identidade do ASP.NET Core.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 4b94cc53777308deb26521a079d8a1c2742744db
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776741"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="70080-104">Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="70080-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="70080-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="70080-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="70080-106">Ver [esse PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="70080-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="70080-107">A versão 1.1 do ASP.NET Core deste tutorial está [nesta](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) pasta.</span><span class="sxs-lookup"><span data-stu-id="70080-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="70080-108">O exemplo do ASP.NET Core 1.1 está em [exemplos](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="70080-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="70080-109">Consulte [esse pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="70080-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70080-110">Este tutorial mostra como criar um aplicativo web do ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="70080-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="70080-111">Ele exibe uma lista de contatos criada por usuários autenticados (registrados).</span><span class="sxs-lookup"><span data-stu-id="70080-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="70080-112">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="70080-112">There are three security groups:</span></span>

* <span data-ttu-id="70080-113">**Usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="70080-114">**Gerenciadores de** podem aprovar ou rejeitar dados de um contato.</span><span class="sxs-lookup"><span data-stu-id="70080-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="70080-115">Apenas os contatos aprovados são visíveis aos usuários.</span><span class="sxs-lookup"><span data-stu-id="70080-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="70080-116">**Os administradores** podem rejeitar/aprovar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="70080-117">As imagens neste documento não correspondem exatamente aos modelos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="70080-117">The images in this document exactly don't match the latest templates.</span></span>

<span data-ttu-id="70080-118">Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado.</span><span class="sxs-lookup"><span data-stu-id="70080-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="70080-119">Rick só pode ver os contatos aprovados e os links **editar**/**excluir**/**criar novo** de seus contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="70080-120">Somente o último registro criado por Rick exibe os links **editar** e **excluir**.</span><span class="sxs-lookup"><span data-stu-id="70080-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="70080-121">Outros usuários não verão o último registro até que um gerente ou administrador altere o status para "Aprovado".</span><span class="sxs-lookup"><span data-stu-id="70080-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="70080-123">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="70080-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

<span data-ttu-id="70080-125">A imagem a seguir mostra a tela de exibição de detalhes de um contato dos gerentes:</span><span class="sxs-lookup"><span data-stu-id="70080-125">The following image shows the managers details view of a contact:</span></span>

![Modo de exibição do Gerenciador de um contato](secure-data/_static/manager.png)

<span data-ttu-id="70080-127">O botões **aprovar** e **rejeitar** são exibidos somente para administradores e gerentes.</span><span class="sxs-lookup"><span data-stu-id="70080-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="70080-128">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="70080-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

<span data-ttu-id="70080-130">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="70080-130">The administrator has all privileges.</span></span> <span data-ttu-id="70080-131">Ele pode ler/editar/excluir todos os contatos e alterar os status deles.</span><span class="sxs-lookup"><span data-stu-id="70080-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="70080-132">O aplicativo foi criado fazendo [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) do seguinte modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="70080-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="70080-133">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="70080-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="70080-134">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="70080-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="70080-135">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="70080-136">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70080-137">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="70080-137">Prerequisites</span></span>

<span data-ttu-id="70080-138">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="70080-138">This tutorial is advanced.</span></span> <span data-ttu-id="70080-139">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="70080-139">You should be familiar with:</span></span>

* [<span data-ttu-id="70080-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70080-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="70080-141">Autenticação</span><span class="sxs-lookup"><span data-stu-id="70080-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="70080-142">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="70080-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="70080-143">Autorização</span><span class="sxs-lookup"><span data-stu-id="70080-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="70080-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="70080-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="70080-145">O aplicativo inicial e o concluído</span><span class="sxs-lookup"><span data-stu-id="70080-145">The starter and completed app</span></span>

<span data-ttu-id="70080-146">[Baixe](xref:index#how-to-download-a-sample) as [concluída](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70080-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="70080-147">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="70080-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="70080-148">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="70080-148">The starter app</span></span>

<span data-ttu-id="70080-149">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/).</span><span class="sxs-lookup"><span data-stu-id="70080-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="70080-150">Execute o aplicativo, clique em **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="70080-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="70080-151">Proteger os dados de usuário</span><span class="sxs-lookup"><span data-stu-id="70080-151">Secure user data</span></span>

<span data-ttu-id="70080-152">As seções a seguir têm todas as principais etapas para criar um aplicativo com dados de usuários seguros.</span><span class="sxs-lookup"><span data-stu-id="70080-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="70080-153">Talvez seja útil para fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="70080-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="70080-154">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="70080-154">Tie the contact data to the user</span></span>

<span data-ttu-id="70080-155">Usar o ASP.NET [identidade](xref:security/authentication/identity) ID de usuário para garantir que os usuários pode editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="70080-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="70080-156">Adicione `OwnerID` e `ContactStatus` para o `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="70080-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="70080-157">`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="70080-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="70080-158">O campo `Status` determina se um contato pode ser visto por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="70080-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="70080-159">Criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="70080-159">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="70080-160">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="70080-160">Add Role services to Identity</span></span>

<span data-ttu-id="70080-161">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="70080-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="70080-162">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="70080-162">Require authenticated users</span></span>

<span data-ttu-id="70080-163">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="70080-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="70080-164">Você pode recusar a autenticação em nível de método de ação, controlador ou página Razor com o `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="70080-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="70080-165">Configurar a política de autenticação padrão para exigir que os usuários sejam autenticados protege recém-adicionado páginas do Razor e controladores.</span><span class="sxs-lookup"><span data-stu-id="70080-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="70080-166">Autenticação necessária por padrão é mais segura do que contar com novos controladores e páginas do Razor para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="70080-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="70080-167">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice e privacidade para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="70080-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="70080-168">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="70080-168">Configure the test account</span></span>

<span data-ttu-id="70080-169">A classe `SeedData` cria duas contas: administrador e gerenciador.</span><span class="sxs-lookup"><span data-stu-id="70080-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="70080-170">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="70080-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="70080-171">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="70080-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="70080-172">Se uma senha forte não for especificada, uma exceção é gerada quando `SeedData.Initialize` é chamado.</span><span class="sxs-lookup"><span data-stu-id="70080-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="70080-173">Atualização `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="70080-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="70080-174">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="70080-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="70080-175">Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="70080-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="70080-176">Adicione a ID de usuário de administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="70080-177">Torne um dos contatos "Enviado" e um "Rejeitado".</span><span class="sxs-lookup"><span data-stu-id="70080-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="70080-178">Adicione a ID de usuário e o status para todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="70080-179">Somente um contato é exibido:</span><span class="sxs-lookup"><span data-stu-id="70080-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="70080-180">Criar o proprietário, manager e manipuladores de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="70080-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="70080-181">Criar uma classe `ContactIsOwnerAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="70080-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70080-182">O `ContactIsOwnerAuthorizationHandler` verifica que o usuário que atua em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="70080-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="70080-183">O `ContactIsOwnerAuthorizationHandler` chamadas [contexto. Êxito](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="70080-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="70080-184">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="70080-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="70080-185">Retornar `context.Succeed` quando os requisitos sejam atendidos.</span><span class="sxs-lookup"><span data-stu-id="70080-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="70080-186">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="70080-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="70080-187">`Task.CompletedTask`Não é êxito ou falha&mdash;permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="70080-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="70080-188">Se você precisar fazer explicitamente, retornar [contexto. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="70080-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="70080-189">O aplicativo permite que proprietários de contato possam editar/excluir/criar seus dados.</span><span class="sxs-lookup"><span data-stu-id="70080-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="70080-190">`ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="70080-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="70080-191">Criar um manipulador de autorização do Gerenciador</span><span class="sxs-lookup"><span data-stu-id="70080-191">Create a manager authorization handler</span></span>

<span data-ttu-id="70080-192">Criar uma classe `ContactManagerAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="70080-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70080-193">O `ContactManagerAuthorizationHandler` verifica o usuário atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="70080-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="70080-194">Somente gerentes possam aprovar ou rejeitar as alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="70080-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="70080-195">Crie um manipulador de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="70080-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="70080-196">Criar uma classe `ContactAdministratorsAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="70080-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70080-197">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="70080-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="70080-198">Administrador pode fazer todas as operações.</span><span class="sxs-lookup"><span data-stu-id="70080-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="70080-199">O registro dos manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="70080-199">Register the authorization handlers</span></span>

<span data-ttu-id="70080-200">Serviços usando o Entity Framework Core devem ser registrados [injeção de dependência](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="70080-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="70080-201">O `ContactIsOwnerAuthorizationHandler` usa o ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia no Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="70080-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="70080-202">O registro dos manipuladores com a coleção de serviço para que eles estejam disponíveis para o `ContactsController` por meio [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="70080-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="70080-203">Adicione o seguinte código ao final da `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70080-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="70080-204">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="70080-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="70080-205">Eles são singletons porque eles não usam o EF e todas as informações necessárias na `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="70080-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="70080-206">Suporte à autorização</span><span class="sxs-lookup"><span data-stu-id="70080-206">Support authorization</span></span>

<span data-ttu-id="70080-207">Nesta seção, você atualize as páginas Razor e adicione uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="70080-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="70080-208">Examine a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="70080-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="70080-209">Examine o `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="70080-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="70080-210">Essa classe contém os requisitos de aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="70080-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="70080-211">Criar uma classe base para as páginas do Razor de contatos</span><span class="sxs-lookup"><span data-stu-id="70080-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="70080-212">Crie uma classe base que contém os serviços usados em contatos páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="70080-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="70080-213">A classe base coloca o código de inicialização em um único local:</span><span class="sxs-lookup"><span data-stu-id="70080-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="70080-214">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="70080-214">The preceding code:</span></span>

* <span data-ttu-id="70080-215">Adiciona o serviço `IAuthorizationService` para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="70080-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="70080-216">Adiciona o serviço de identidade `UserManager`.</span><span class="sxs-lookup"><span data-stu-id="70080-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="70080-217">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="70080-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="70080-218">Atualizar o CreateModel</span><span class="sxs-lookup"><span data-stu-id="70080-218">Update the CreateModel</span></span>

<span data-ttu-id="70080-219">Atualize o construtor de criar modelo de página para usar a classe base `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="70080-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="70080-220">Atualize o método `CreateModel.OnPostAsync` para:</span><span class="sxs-lookup"><span data-stu-id="70080-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="70080-221">Adicione a ID de usuário ao modelo `Contact`.</span><span class="sxs-lookup"><span data-stu-id="70080-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="70080-222">Chame o manipulador de autorização para verificar se que o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="70080-223">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="70080-223">Update the IndexModel</span></span>

<span data-ttu-id="70080-224">Atualize o método `OnGetAsync` para que apenas os contatos aprovados sejam mostrados aos usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="70080-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="70080-225">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="70080-225">Update the EditModel</span></span>

<span data-ttu-id="70080-226">Adicione um manipulador de autorização para verificar se que o usuário é proprietária do contato.</span><span class="sxs-lookup"><span data-stu-id="70080-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="70080-227">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="70080-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="70080-228">O aplicativo não tiver acesso ao recurso quando atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="70080-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="70080-229">Autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="70080-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="70080-230">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, carregá-los no modelo de página ou carregá-los dentro do manipulador em si.</span><span class="sxs-lookup"><span data-stu-id="70080-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="70080-231">Com frequência, você acessa o recurso, passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="70080-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="70080-232">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="70080-232">Update the DeleteModel</span></span>

<span data-ttu-id="70080-233">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se que o usuário tem permissão de exclusão no contato.</span><span class="sxs-lookup"><span data-stu-id="70080-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="70080-234">Injetar o serviço de autorização em exibições</span><span class="sxs-lookup"><span data-stu-id="70080-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="70080-235">Atualmente, mostra a interface do usuário a edita e excluir links para os contatos, que o usuário não é possível modificar.</span><span class="sxs-lookup"><span data-stu-id="70080-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="70080-236">Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="70080-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="70080-237">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="70080-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="70080-238">Atualizar o **editar** e **excluir** vincula na *Pages/Contacts/Index.cshtml* para que eles sejam renderizados somente para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="70080-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="70080-239">Ocultar links de usuários que não tem permissão para alterar os dados não proteger o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70080-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="70080-240">Ocultar links torna o aplicativo mais fácil de usar, exibindo links só é válidas.</span><span class="sxs-lookup"><span data-stu-id="70080-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="70080-241">Os usuários podem hack gerados URLs para invocar editar e excluir operações em dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="70080-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="70080-242">A página do Razor ou controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="70080-243">Detalhes da atualização</span><span class="sxs-lookup"><span data-stu-id="70080-243">Update Details</span></span>

<span data-ttu-id="70080-244">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="70080-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="70080-245">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="70080-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="70080-246">Adicionar ou remover um usuário a uma função</span><span class="sxs-lookup"><span data-stu-id="70080-246">Add or remove a user to a role</span></span>

<span data-ttu-id="70080-247">Ver [esse problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="70080-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="70080-248">Remoção de privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="70080-248">Removing privileges from a user.</span></span> <span data-ttu-id="70080-249">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="70080-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="70080-250">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="70080-250">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="70080-251">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="70080-251">Test the completed app</span></span>

<span data-ttu-id="70080-252">Se você ainda não tiver configurado uma senha para contas de usuário propagados, use o [ferramenta Secret Manager](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="70080-252">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="70080-253">Escolha uma senha forte: Use oito ou mais caracteres e pelo menos um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="70080-253">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="70080-254">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="70080-254">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="70080-255">Execute o seguinte comando na pasta do projeto, onde `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="70080-255">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="70080-256">Se o aplicativo tem contatos:</span><span class="sxs-lookup"><span data-stu-id="70080-256">If the app has contacts:</span></span>

* <span data-ttu-id="70080-257">Excluir todos os registros no `Contact` tabela.</span><span class="sxs-lookup"><span data-stu-id="70080-257">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="70080-258">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="70080-258">Restart the app to seed the database.</span></span>

<span data-ttu-id="70080-259">Uma maneira fácil de testar o aplicativo concluído é iniciar três diferentes navegadores (ou incógnita/InPrivate sessões).</span><span class="sxs-lookup"><span data-stu-id="70080-259">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="70080-260">Em um navegador, registre um novo usuário (por exemplo, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="70080-260">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="70080-261">Entrar para cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="70080-261">Sign in to each browser with a different user.</span></span> <span data-ttu-id="70080-262">Verifique se as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="70080-262">Verify the following operations:</span></span>

* <span data-ttu-id="70080-263">Usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="70080-263">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="70080-264">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-264">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="70080-265">Os gerentes podem Aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="70080-265">Managers can approve/reject contact data.</span></span> <span data-ttu-id="70080-266">A tela `Details` mostra os botões **aprovar** e **rejeitar**.</span><span class="sxs-lookup"><span data-stu-id="70080-266">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="70080-267">Os administradores podem Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-267">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="70080-268">User</span><span class="sxs-lookup"><span data-stu-id="70080-268">User</span></span>                | <span data-ttu-id="70080-269">Propagada pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="70080-269">Seeded by the app</span></span> | <span data-ttu-id="70080-270">Opções</span><span class="sxs-lookup"><span data-stu-id="70080-270">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="70080-271">Não</span><span class="sxs-lookup"><span data-stu-id="70080-271">No</span></span>                | <span data-ttu-id="70080-272">Editar/Excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-272">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="70080-273">Sim</span><span class="sxs-lookup"><span data-stu-id="70080-273">Yes</span></span>               | <span data-ttu-id="70080-274">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-274">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="70080-275">Sim</span><span class="sxs-lookup"><span data-stu-id="70080-275">Yes</span></span>               | <span data-ttu-id="70080-276">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-276">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="70080-277">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="70080-277">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="70080-278">Copie a URL para excluir e editar a partir do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="70080-278">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="70080-279">Cole esses links no navegador do usuário de teste para verificar se que o usuário de teste não é possível executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="70080-279">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="70080-280">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="70080-280">Create the starter app</span></span>

* <span data-ttu-id="70080-281">Criar um aplicativo páginas Razor denominado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="70080-281">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="70080-282">Criar o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="70080-282">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="70080-283">O nome "ContactManager" para o namespace coincida com o namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="70080-283">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="70080-284">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="70080-284">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="70080-285">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="70080-285">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="70080-286">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="70080-286">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="70080-287">Criar a migração inicial e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="70080-287">Create initial migration and update the database:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
  ```

<span data-ttu-id="70080-288">Se você tiver um bug com o `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="70080-288">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="70080-289">Atualize a âncora ContactManager no arquivo *pages/Shared/_ layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="70080-289">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="70080-290">Testar o aplicativo criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="70080-290">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="70080-291">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="70080-291">Seed the database</span></span>

<span data-ttu-id="70080-292">Adicione a classe [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :</span><span class="sxs-lookup"><span data-stu-id="70080-292">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="70080-293">Chame `SeedData.Initialize` de `Main`:</span><span class="sxs-lookup"><span data-stu-id="70080-293">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="70080-294">Se o aplicativo propagado o banco de dados de teste.</span><span class="sxs-lookup"><span data-stu-id="70080-294">Test that the app seeded the database.</span></span> <span data-ttu-id="70080-295">Se não houver nenhuma linha no banco de dados de contato, o método de propagação não será executado.</span><span class="sxs-lookup"><span data-stu-id="70080-295">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="70080-296">Este tutorial mostra como criar um aplicativo web do ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="70080-296">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="70080-297">Ele exibe uma lista de contatos criada por usuários autenticados (registrados).</span><span class="sxs-lookup"><span data-stu-id="70080-297">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="70080-298">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="70080-298">There are three security groups:</span></span>

* <span data-ttu-id="70080-299">**Usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-299">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="70080-300">**Gerenciadores de** podem aprovar ou rejeitar dados de um contato.</span><span class="sxs-lookup"><span data-stu-id="70080-300">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="70080-301">Apenas os contatos aprovados são visíveis aos usuários.</span><span class="sxs-lookup"><span data-stu-id="70080-301">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="70080-302">**Os administradores** podem rejeitar/aprovar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-302">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="70080-303">Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado.</span><span class="sxs-lookup"><span data-stu-id="70080-303">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="70080-304">Rick só pode ver os contatos aprovados e os links **editar**/**excluir**/**criar novo** de seus contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-304">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="70080-305">Somente o último registro criado por Rick exibe os links **editar** e **excluir**.</span><span class="sxs-lookup"><span data-stu-id="70080-305">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="70080-306">Outros usuários não verão o último registro até que um gerente ou administrador altere o status para "Aprovado".</span><span class="sxs-lookup"><span data-stu-id="70080-306">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="70080-308">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="70080-308">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

<span data-ttu-id="70080-310">A imagem a seguir mostra a tela de exibição de detalhes de um contato dos gerentes:</span><span class="sxs-lookup"><span data-stu-id="70080-310">The following image shows the managers details view of a contact:</span></span>

![Modo de exibição do Gerenciador de um contato](secure-data/_static/manager.png)

<span data-ttu-id="70080-312">O botões **aprovar** e **rejeitar** são exibidos somente para administradores e gerentes.</span><span class="sxs-lookup"><span data-stu-id="70080-312">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="70080-313">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="70080-313">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

<span data-ttu-id="70080-315">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="70080-315">The administrator has all privileges.</span></span> <span data-ttu-id="70080-316">Ele pode ler/editar/excluir todos os contatos e alterar os status deles.</span><span class="sxs-lookup"><span data-stu-id="70080-316">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="70080-317">O aplicativo foi criado fazendo [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) do seguinte modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="70080-317">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="70080-318">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="70080-318">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="70080-319">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="70080-319">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="70080-320">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-320">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="70080-321">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-321">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70080-322">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="70080-322">Prerequisites</span></span>

<span data-ttu-id="70080-323">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="70080-323">This tutorial is advanced.</span></span> <span data-ttu-id="70080-324">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="70080-324">You should be familiar with:</span></span>

* [<span data-ttu-id="70080-325">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70080-325">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="70080-326">Autenticação</span><span class="sxs-lookup"><span data-stu-id="70080-326">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="70080-327">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="70080-327">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="70080-328">Autorização</span><span class="sxs-lookup"><span data-stu-id="70080-328">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="70080-329">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="70080-329">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="70080-330">O aplicativo inicial e o concluído</span><span class="sxs-lookup"><span data-stu-id="70080-330">The starter and completed app</span></span>

<span data-ttu-id="70080-331">[Baixe](xref:index#how-to-download-a-sample) as [concluída](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70080-331">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="70080-332">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="70080-332">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="70080-333">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="70080-333">The starter app</span></span>

<span data-ttu-id="70080-334">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/).</span><span class="sxs-lookup"><span data-stu-id="70080-334">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="70080-335">Execute o aplicativo, clique em **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="70080-335">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="70080-336">Proteger os dados de usuário</span><span class="sxs-lookup"><span data-stu-id="70080-336">Secure user data</span></span>

<span data-ttu-id="70080-337">As seções a seguir têm todas as principais etapas para criar um aplicativo com dados de usuários seguros.</span><span class="sxs-lookup"><span data-stu-id="70080-337">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="70080-338">Talvez seja útil para fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="70080-338">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="70080-339">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="70080-339">Tie the contact data to the user</span></span>

<span data-ttu-id="70080-340">Usar o ASP.NET [identidade](xref:security/authentication/identity) ID de usuário para garantir que os usuários pode editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="70080-340">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="70080-341">Adicione `OwnerID` e `ContactStatus` para o `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="70080-341">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="70080-342">`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="70080-342">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="70080-343">O campo `Status` determina se um contato pode ser visto por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="70080-343">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="70080-344">Criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="70080-344">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="70080-345">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="70080-345">Add Role services to Identity</span></span>

<span data-ttu-id="70080-346">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="70080-346">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="70080-347">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="70080-347">Require authenticated users</span></span>

<span data-ttu-id="70080-348">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="70080-348">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="70080-349">Você pode recusar a autenticação em nível de método de ação, controlador ou página Razor com o `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="70080-349">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="70080-350">Configurar a política de autenticação padrão para exigir que os usuários sejam autenticados protege recém-adicionado páginas do Razor e controladores.</span><span class="sxs-lookup"><span data-stu-id="70080-350">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="70080-351">Autenticação necessária por padrão é mais segura do que contar com novos controladores e páginas do Razor para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="70080-351">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="70080-352">Adicionar [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)às páginas Índice, Sobre e Contatos para que usuários anônimos possam obter informações sobre o site antes de eles se registrarem.</span><span class="sxs-lookup"><span data-stu-id="70080-352">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="70080-353">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="70080-353">Configure the test account</span></span>

<span data-ttu-id="70080-354">A classe `SeedData` cria duas contas: administrador e gerenciador.</span><span class="sxs-lookup"><span data-stu-id="70080-354">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="70080-355">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="70080-355">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="70080-356">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="70080-356">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="70080-357">Se uma senha forte não for especificada, uma exceção é gerada quando `SeedData.Initialize` é chamado.</span><span class="sxs-lookup"><span data-stu-id="70080-357">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="70080-358">Atualização `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="70080-358">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="70080-359">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="70080-359">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="70080-360">Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="70080-360">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="70080-361">Adicione a ID de usuário de administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-361">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="70080-362">Torne um dos contatos "Enviado" e um "Rejeitado".</span><span class="sxs-lookup"><span data-stu-id="70080-362">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="70080-363">Adicione a ID de usuário e o status para todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-363">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="70080-364">Somente um contato é exibido:</span><span class="sxs-lookup"><span data-stu-id="70080-364">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="70080-365">Criar o proprietário, manager e manipuladores de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="70080-365">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="70080-366">Criar uma classe `ContactIsOwnerAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="70080-366">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70080-367">O `ContactIsOwnerAuthorizationHandler` verifica que o usuário que atua em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="70080-367">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="70080-368">O `ContactIsOwnerAuthorizationHandler` chamadas [contexto. Êxito](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="70080-368">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="70080-369">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="70080-369">Authorization handlers generally:</span></span>

* <span data-ttu-id="70080-370">Retornar `context.Succeed` quando os requisitos sejam atendidos.</span><span class="sxs-lookup"><span data-stu-id="70080-370">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="70080-371">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="70080-371">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="70080-372">`Task.CompletedTask`Não é êxito ou falha&mdash;permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="70080-372">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="70080-373">Se você precisar fazer explicitamente, retornar [contexto. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="70080-373">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="70080-374">O aplicativo permite que proprietários de contato possam editar/excluir/criar seus dados.</span><span class="sxs-lookup"><span data-stu-id="70080-374">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="70080-375">`ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="70080-375">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="70080-376">Criar um manipulador de autorização do Gerenciador</span><span class="sxs-lookup"><span data-stu-id="70080-376">Create a manager authorization handler</span></span>

<span data-ttu-id="70080-377">Criar uma classe `ContactManagerAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="70080-377">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70080-378">O `ContactManagerAuthorizationHandler` verifica o usuário atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="70080-378">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="70080-379">Somente gerentes possam aprovar ou rejeitar as alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="70080-379">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="70080-380">Crie um manipulador de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="70080-380">Create an administrator authorization handler</span></span>

<span data-ttu-id="70080-381">Criar uma classe `ContactAdministratorsAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="70080-381">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70080-382">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="70080-382">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="70080-383">Administrador pode fazer todas as operações.</span><span class="sxs-lookup"><span data-stu-id="70080-383">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="70080-384">O registro dos manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="70080-384">Register the authorization handlers</span></span>

<span data-ttu-id="70080-385">Serviços usando o Entity Framework Core devem ser registrados [injeção de dependência](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="70080-385">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="70080-386">O `ContactIsOwnerAuthorizationHandler` usa o ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia no Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="70080-386">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="70080-387">O registro dos manipuladores com a coleção de serviço para que eles estejam disponíveis para o `ContactsController` por meio [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="70080-387">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="70080-388">Adicione o seguinte código ao final da `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70080-388">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="70080-389">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="70080-389">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="70080-390">Eles são singletons porque eles não usam o EF e todas as informações necessárias na `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="70080-390">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="70080-391">Suporte à autorização</span><span class="sxs-lookup"><span data-stu-id="70080-391">Support authorization</span></span>

<span data-ttu-id="70080-392">Nesta seção, você atualize as páginas Razor e adicione uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="70080-392">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="70080-393">Examine a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="70080-393">Review the contact operations requirements class</span></span>

<span data-ttu-id="70080-394">Examine o `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="70080-394">Review the `ContactOperations` class.</span></span> <span data-ttu-id="70080-395">Essa classe contém os requisitos de aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="70080-395">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="70080-396">Criar uma classe base para as páginas do Razor de contatos</span><span class="sxs-lookup"><span data-stu-id="70080-396">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="70080-397">Crie uma classe base que contém os serviços usados em contatos páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="70080-397">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="70080-398">A classe base coloca o código de inicialização em um único local:</span><span class="sxs-lookup"><span data-stu-id="70080-398">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="70080-399">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="70080-399">The preceding code:</span></span>

* <span data-ttu-id="70080-400">Adiciona o serviço `IAuthorizationService` para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="70080-400">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="70080-401">Adiciona o serviço de identidade `UserManager`.</span><span class="sxs-lookup"><span data-stu-id="70080-401">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="70080-402">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="70080-402">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="70080-403">Atualizar o CreateModel</span><span class="sxs-lookup"><span data-stu-id="70080-403">Update the CreateModel</span></span>

<span data-ttu-id="70080-404">Atualize o construtor de criar modelo de página para usar a classe base `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="70080-404">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="70080-405">Atualize o método `CreateModel.OnPostAsync` para:</span><span class="sxs-lookup"><span data-stu-id="70080-405">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="70080-406">Adicione a ID de usuário ao modelo `Contact`.</span><span class="sxs-lookup"><span data-stu-id="70080-406">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="70080-407">Chame o manipulador de autorização para verificar se que o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="70080-407">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="70080-408">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="70080-408">Update the IndexModel</span></span>

<span data-ttu-id="70080-409">Atualize o método `OnGetAsync` para que apenas os contatos aprovados sejam mostrados aos usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="70080-409">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="70080-410">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="70080-410">Update the EditModel</span></span>

<span data-ttu-id="70080-411">Adicione um manipulador de autorização para verificar se que o usuário é proprietária do contato.</span><span class="sxs-lookup"><span data-stu-id="70080-411">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="70080-412">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="70080-412">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="70080-413">O aplicativo não tiver acesso ao recurso quando atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="70080-413">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="70080-414">Autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="70080-414">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="70080-415">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, carregá-los no modelo de página ou carregá-los dentro do manipulador em si.</span><span class="sxs-lookup"><span data-stu-id="70080-415">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="70080-416">Com frequência, você acessa o recurso, passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="70080-416">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="70080-417">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="70080-417">Update the DeleteModel</span></span>

<span data-ttu-id="70080-418">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se que o usuário tem permissão de exclusão no contato.</span><span class="sxs-lookup"><span data-stu-id="70080-418">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="70080-419">Injetar o serviço de autorização em exibições</span><span class="sxs-lookup"><span data-stu-id="70080-419">Inject the authorization service into the views</span></span>

<span data-ttu-id="70080-420">Atualmente, mostra a interface do usuário a edita e excluir links para os contatos, que o usuário não é possível modificar.</span><span class="sxs-lookup"><span data-stu-id="70080-420">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="70080-421">Injetar o serviço de autorização no arquivo *Views/_ViewImports.cshtml* para que ele esteja disponível para todos os modos de exibição:</span><span class="sxs-lookup"><span data-stu-id="70080-421">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="70080-422">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="70080-422">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="70080-423">Atualizar o **editar** e **excluir** vincula na *Pages/Contacts/Index.cshtml* para que eles sejam renderizados somente para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="70080-423">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="70080-424">Ocultar links de usuários que não tem permissão para alterar os dados não proteger o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70080-424">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="70080-425">Ocultar links torna o aplicativo mais fácil de usar, exibindo links só é válidas.</span><span class="sxs-lookup"><span data-stu-id="70080-425">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="70080-426">Os usuários podem hack gerados URLs para invocar editar e excluir operações em dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="70080-426">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="70080-427">A página do Razor ou controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-427">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="70080-428">Detalhes da atualização</span><span class="sxs-lookup"><span data-stu-id="70080-428">Update Details</span></span>

<span data-ttu-id="70080-429">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="70080-429">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="70080-430">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="70080-430">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="70080-431">Adicionar ou remover um usuário a uma função</span><span class="sxs-lookup"><span data-stu-id="70080-431">Add or remove a user to a role</span></span>

<span data-ttu-id="70080-432">Ver [esse problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="70080-432">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="70080-433">Remoção de privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="70080-433">Removing privileges from a user.</span></span> <span data-ttu-id="70080-434">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="70080-434">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="70080-435">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="70080-435">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="70080-436">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="70080-436">Test the completed app</span></span>

<span data-ttu-id="70080-437">Se você ainda não tiver configurado uma senha para contas de usuário propagados, use o [ferramenta Secret Manager](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="70080-437">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="70080-438">Escolha uma senha forte: Use oito ou mais caracteres e pelo menos um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="70080-438">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="70080-439">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="70080-439">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="70080-440">Execute o seguinte comando na pasta do projeto, onde `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="70080-440">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="70080-441">Remover e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="70080-441">Drop and update the Database</span></span>

    ```console
     dotnet ef database drop -f
     dotnet ef database update  
     ```

* <span data-ttu-id="70080-442">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="70080-442">Restart the app to seed the database.</span></span>

<span data-ttu-id="70080-443">Uma maneira fácil de testar o aplicativo concluído é iniciar três diferentes navegadores (ou incógnita/InPrivate sessões).</span><span class="sxs-lookup"><span data-stu-id="70080-443">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="70080-444">Em um navegador, registre um novo usuário (por exemplo, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="70080-444">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="70080-445">Entrar para cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="70080-445">Sign in to each browser with a different user.</span></span> <span data-ttu-id="70080-446">Verifique se as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="70080-446">Verify the following operations:</span></span>

* <span data-ttu-id="70080-447">Usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="70080-447">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="70080-448">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-448">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="70080-449">Os gerentes podem Aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="70080-449">Managers can approve/reject contact data.</span></span> <span data-ttu-id="70080-450">A tela `Details` mostra os botões **aprovar** e **rejeitar**.</span><span class="sxs-lookup"><span data-stu-id="70080-450">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="70080-451">Os administradores podem Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-451">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="70080-452">User</span><span class="sxs-lookup"><span data-stu-id="70080-452">User</span></span>                | <span data-ttu-id="70080-453">Propagada pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="70080-453">Seeded by the app</span></span> | <span data-ttu-id="70080-454">Opções</span><span class="sxs-lookup"><span data-stu-id="70080-454">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="70080-455">Não</span><span class="sxs-lookup"><span data-stu-id="70080-455">No</span></span>                | <span data-ttu-id="70080-456">Editar/Excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-456">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="70080-457">Sim</span><span class="sxs-lookup"><span data-stu-id="70080-457">Yes</span></span>               | <span data-ttu-id="70080-458">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="70080-458">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="70080-459">Sim</span><span class="sxs-lookup"><span data-stu-id="70080-459">Yes</span></span>               | <span data-ttu-id="70080-460">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="70080-460">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="70080-461">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="70080-461">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="70080-462">Copie a URL para excluir e editar a partir do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="70080-462">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="70080-463">Cole esses links no navegador do usuário de teste para verificar se que o usuário de teste não é possível executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="70080-463">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="70080-464">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="70080-464">Create the starter app</span></span>

* <span data-ttu-id="70080-465">Criar um aplicativo páginas Razor denominado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="70080-465">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="70080-466">Criar o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="70080-466">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="70080-467">O nome "ContactManager" para o namespace coincida com o namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="70080-467">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="70080-468">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="70080-468">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="70080-469">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="70080-469">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="70080-470">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="70080-470">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="70080-471">Criar a migração inicial e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="70080-471">Create initial migration and update the database:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="70080-472">Atualizar o link de **ContactManager** no arquivo *Pages/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70080-472">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="70080-473">Testar o aplicativo criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="70080-473">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="70080-474">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="70080-474">Seed the database</span></span>

<span data-ttu-id="70080-475">Adicione a [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) de classe para o *dados* pasta.</span><span class="sxs-lookup"><span data-stu-id="70080-475">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="70080-476">Chame `SeedData.Initialize` de `Main`:</span><span class="sxs-lookup"><span data-stu-id="70080-476">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="70080-477">Se o aplicativo propagado o banco de dados de teste.</span><span class="sxs-lookup"><span data-stu-id="70080-477">Test that the app seeded the database.</span></span> <span data-ttu-id="70080-478">Se não houver nenhuma linha no banco de dados de contato, o método de propagação não será executado.</span><span class="sxs-lookup"><span data-stu-id="70080-478">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="70080-479">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="70080-479">Additional resources</span></span>

* [<span data-ttu-id="70080-480">Criar um aplicativo web .NET Core e o banco de dados SQL no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="70080-480">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="70080-481">[Laboratório de autorização do ASP.NET Core](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="70080-481">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="70080-482">Este laboratório apresenta mais detalhes sobre os recursos de segurança introduzidos neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="70080-482">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="70080-483">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="70080-483">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
