---
title: Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização
author: rick-anderson
description: Saiba como criar um aplicativo páginas Razor com dados protegidos por autorização do usuário. Inclui HTTPS, autenticação, segurança, identidade do ASP.NET Core.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 65c72d4dd457f85451796c5713bedebafec7a7de
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239829"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="646d7-104">Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="646d7-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="646d7-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="646d7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="646d7-106">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="646d7-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="646d7-107">A versão ASP.NET Core 1,1 deste tutorial está [nesta pasta.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)</span><span class="sxs-lookup"><span data-stu-id="646d7-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="646d7-108">O exemplo de ASP.NET Core de 1,1 está nos [exemplos](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="646d7-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="646d7-109">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="646d7-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="646d7-110">Este tutorial mostra como criar um aplicativo web do ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="646d7-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="646d7-111">Ele exibe uma lista de contatos criada por usuários autenticados (registrados).</span><span class="sxs-lookup"><span data-stu-id="646d7-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="646d7-112">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="646d7-112">There are three security groups:</span></span>

* <span data-ttu-id="646d7-113">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="646d7-114">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="646d7-115">Apenas os contatos aprovados são visíveis aos usuários.</span><span class="sxs-lookup"><span data-stu-id="646d7-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="646d7-116">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="646d7-117">As imagens neste documento não correspondem exatamente aos modelos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="646d7-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="646d7-118">Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado.</span><span class="sxs-lookup"><span data-stu-id="646d7-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="646d7-119">Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="646d7-120">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="646d7-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="646d7-121">Outros usuários não verão o último registro até que um gerente ou administrador altere o status para "Aprovado".</span><span class="sxs-lookup"><span data-stu-id="646d7-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="646d7-123">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="646d7-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

<span data-ttu-id="646d7-125">A imagem a seguir mostra a tela de exibição de detalhes de um contato dos gerentes:</span><span class="sxs-lookup"><span data-stu-id="646d7-125">The following image shows the managers details view of a contact:</span></span>

![Modo de exibição do Gerenciador de um contato](secure-data/_static/manager.png)

<span data-ttu-id="646d7-127">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="646d7-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="646d7-128">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="646d7-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

<span data-ttu-id="646d7-130">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="646d7-130">The administrator has all privileges.</span></span> <span data-ttu-id="646d7-131">Ele pode ler/editar/excluir todos os contatos e alterar os status deles.</span><span class="sxs-lookup"><span data-stu-id="646d7-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="646d7-132">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="646d7-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="646d7-133">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="646d7-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="646d7-134">`ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="646d7-135">`ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="646d7-136">`ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="646d7-137">{1&gt;Pré-requisitos&lt;1}</span><span class="sxs-lookup"><span data-stu-id="646d7-137">Prerequisites</span></span>

<span data-ttu-id="646d7-138">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="646d7-138">This tutorial is advanced.</span></span> <span data-ttu-id="646d7-139">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="646d7-139">You should be familiar with:</span></span>

* [<span data-ttu-id="646d7-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="646d7-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="646d7-141">Autenticação</span><span class="sxs-lookup"><span data-stu-id="646d7-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="646d7-142">Confirmação de conta e de recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="646d7-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="646d7-143">Autorização</span><span class="sxs-lookup"><span data-stu-id="646d7-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="646d7-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="646d7-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="646d7-145">O aplicativo inicial e o concluído</span><span class="sxs-lookup"><span data-stu-id="646d7-145">The starter and completed app</span></span>

<span data-ttu-id="646d7-146">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="646d7-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="646d7-147">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="646d7-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="646d7-148">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="646d7-148">The starter app</span></span>

<span data-ttu-id="646d7-149">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="646d7-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="646d7-150">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="646d7-151">Proteger os dados de usuário</span><span class="sxs-lookup"><span data-stu-id="646d7-151">Secure user data</span></span>

<span data-ttu-id="646d7-152">As seções a seguir têm todas as principais etapas para criar um aplicativo com dados de usuários seguros.</span><span class="sxs-lookup"><span data-stu-id="646d7-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="646d7-153">Talvez seja útil para fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="646d7-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="646d7-154">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="646d7-154">Tie the contact data to the user</span></span>

<span data-ttu-id="646d7-155">Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="646d7-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="646d7-156">Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="646d7-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="646d7-157">`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="646d7-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="646d7-158">O campo `Status` determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="646d7-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="646d7-159">Criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="646d7-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="646d7-160">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="646d7-160">Add Role services to Identity</span></span>

<span data-ttu-id="646d7-161">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="646d7-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="646d7-162">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="646d7-162">Require authenticated users</span></span>

<span data-ttu-id="646d7-163">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="646d7-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="646d7-164">Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="646d7-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="646d7-165">Configurar a política de autenticação padrão para exigir que os usuários sejam autenticados protege recém-adicionado páginas do Razor e controladores.</span><span class="sxs-lookup"><span data-stu-id="646d7-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="646d7-166">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="646d7-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="646d7-167">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice e privacidade para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="646d7-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="646d7-168">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="646d7-168">Configure the test account</span></span>

<span data-ttu-id="646d7-169">A classe `SeedData` cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="646d7-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="646d7-170">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="646d7-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="646d7-171">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="646d7-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="646d7-172">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="646d7-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="646d7-173">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="646d7-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="646d7-174">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="646d7-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="646d7-175">Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="646d7-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="646d7-176">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="646d7-177">Torne um dos contatos "Enviado" e um "Rejeitado".</span><span class="sxs-lookup"><span data-stu-id="646d7-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="646d7-178">Adicione a ID de usuário e o status para todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="646d7-179">Somente um contato é exibido:</span><span class="sxs-lookup"><span data-stu-id="646d7-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="646d7-180">Criar o proprietário, manager e manipuladores de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="646d7-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="646d7-181">Crie uma classe de `ContactIsOwnerAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="646d7-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="646d7-182">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="646d7-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="646d7-183">O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="646d7-184">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="646d7-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="646d7-185">Retorne `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="646d7-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="646d7-186">Retorne `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="646d7-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="646d7-187">`Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="646d7-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="646d7-188">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="646d7-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="646d7-189">O aplicativo permite que proprietários de contato possam editar/excluir/criar seus dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="646d7-190">`ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="646d7-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="646d7-191">Criar um manipulador de autorização do Gerenciador</span><span class="sxs-lookup"><span data-stu-id="646d7-191">Create a manager authorization handler</span></span>

<span data-ttu-id="646d7-192">Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="646d7-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="646d7-193">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="646d7-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="646d7-194">Somente gerentes possam aprovar ou rejeitar as alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="646d7-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="646d7-195">Crie um manipulador de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="646d7-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="646d7-196">Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="646d7-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="646d7-197">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="646d7-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="646d7-198">Administrador pode fazer todas as operações.</span><span class="sxs-lookup"><span data-stu-id="646d7-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="646d7-199">O registro dos manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="646d7-199">Register the authorization handlers</span></span>

<span data-ttu-id="646d7-200">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="646d7-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="646d7-201">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="646d7-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="646d7-202">Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="646d7-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="646d7-203">Adicione o seguinte código ao final do `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="646d7-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="646d7-204">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="646d7-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="646d7-205">Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.</span><span class="sxs-lookup"><span data-stu-id="646d7-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="646d7-206">Suporte à autorização</span><span class="sxs-lookup"><span data-stu-id="646d7-206">Support authorization</span></span>

<span data-ttu-id="646d7-207">Nesta seção, você atualize as páginas Razor e adicione uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="646d7-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="646d7-208">Examine a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="646d7-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="646d7-209">Examine a classe `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="646d7-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="646d7-210">Essa classe contém os requisitos de aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="646d7-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="646d7-211">Criar uma classe base para as páginas do Razor de contatos</span><span class="sxs-lookup"><span data-stu-id="646d7-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="646d7-212">Crie uma classe base que contém os serviços usados em contatos páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="646d7-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="646d7-213">A classe base coloca o código de inicialização em um único local:</span><span class="sxs-lookup"><span data-stu-id="646d7-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="646d7-214">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="646d7-214">The preceding code:</span></span>

* <span data-ttu-id="646d7-215">Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="646d7-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="646d7-216">Adiciona o serviço de `UserManager` de identidade.</span><span class="sxs-lookup"><span data-stu-id="646d7-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="646d7-217">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="646d7-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="646d7-218">Atualizar o CreateModel</span><span class="sxs-lookup"><span data-stu-id="646d7-218">Update the CreateModel</span></span>

<span data-ttu-id="646d7-219">Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="646d7-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="646d7-220">Atualize o método `CreateModel.OnPostAsync` para:</span><span class="sxs-lookup"><span data-stu-id="646d7-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="646d7-221">Adicione a ID de usuário ao modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="646d7-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="646d7-222">Chame o manipulador de autorização para verificar se que o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="646d7-223">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="646d7-223">Update the IndexModel</span></span>

<span data-ttu-id="646d7-224">Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="646d7-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="646d7-225">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="646d7-225">Update the EditModel</span></span>

<span data-ttu-id="646d7-226">Adicione um manipulador de autorização para verificar se que o usuário é proprietária do contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="646d7-227">Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="646d7-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="646d7-228">O aplicativo não tiver acesso ao recurso quando atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="646d7-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="646d7-229">Autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="646d7-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="646d7-230">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, carregá-los no modelo de página ou carregá-los dentro do manipulador em si.</span><span class="sxs-lookup"><span data-stu-id="646d7-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="646d7-231">Com frequência, você acessa o recurso, passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="646d7-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="646d7-232">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="646d7-232">Update the DeleteModel</span></span>

<span data-ttu-id="646d7-233">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se que o usuário tem permissão de exclusão no contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="646d7-234">Injetar o serviço de autorização em exibições</span><span class="sxs-lookup"><span data-stu-id="646d7-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="646d7-235">Atualmente, mostra a interface do usuário a edita e excluir links para os contatos, que o usuário não é possível modificar.</span><span class="sxs-lookup"><span data-stu-id="646d7-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="646d7-236">Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="646d7-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="646d7-237">A marcação anterior adiciona várias instruções `using`.</span><span class="sxs-lookup"><span data-stu-id="646d7-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="646d7-238">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="646d7-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="646d7-239">Ocultar links de usuários que não tem permissão para alterar os dados não proteger o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="646d7-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="646d7-240">Ocultar links torna o aplicativo mais fácil de usar, exibindo links só é válidas.</span><span class="sxs-lookup"><span data-stu-id="646d7-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="646d7-241">Os usuários podem hack gerados URLs para invocar editar e excluir operações em dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="646d7-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="646d7-242">A página do Razor ou controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="646d7-243">Detalhes da atualização</span><span class="sxs-lookup"><span data-stu-id="646d7-243">Update Details</span></span>

<span data-ttu-id="646d7-244">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="646d7-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="646d7-245">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="646d7-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="646d7-246">Adicionar ou remover um usuário a uma função</span><span class="sxs-lookup"><span data-stu-id="646d7-246">Add or remove a user to a role</span></span>

<span data-ttu-id="646d7-247">Consulte [este problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="646d7-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="646d7-248">Remoção de privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="646d7-248">Removing privileges from a user.</span></span> <span data-ttu-id="646d7-249">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="646d7-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="646d7-250">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="646d7-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="646d7-251">Diferenças entre desafio e proíba</span><span class="sxs-lookup"><span data-stu-id="646d7-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="646d7-252">Esse aplicativo define a política padrão para [exigir usuários autenticados](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="646d7-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="646d7-253">O código a seguir permite usuários anônimos.</span><span class="sxs-lookup"><span data-stu-id="646d7-253">The following code allows anonymous users.</span></span> <span data-ttu-id="646d7-254">Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.</span><span class="sxs-lookup"><span data-stu-id="646d7-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="646d7-255">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="646d7-255">In the preceding code:</span></span>

* <span data-ttu-id="646d7-256">Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="646d7-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="646d7-257">Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.</span><span class="sxs-lookup"><span data-stu-id="646d7-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="646d7-258">Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="646d7-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="646d7-259">Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.</span><span class="sxs-lookup"><span data-stu-id="646d7-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="646d7-260">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="646d7-260">Test the completed app</span></span>

<span data-ttu-id="646d7-261">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="646d7-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="646d7-262">Escolha uma senha forte: Use oito ou mais caracteres e pelo menos um caractere maiusculo, número e símbolo.</span><span class="sxs-lookup"><span data-stu-id="646d7-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="646d7-263">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="646d7-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="646d7-264">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="646d7-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="646d7-265">Se o aplicativo tem contatos:</span><span class="sxs-lookup"><span data-stu-id="646d7-265">If the app has contacts:</span></span>

* <span data-ttu-id="646d7-266">Exclua todos os registros na tabela `Contact`.</span><span class="sxs-lookup"><span data-stu-id="646d7-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="646d7-267">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="646d7-268">Uma maneira fácil de testar o aplicativo concluído é iniciar três diferentes navegadores (ou incógnita/InPrivate sessões).</span><span class="sxs-lookup"><span data-stu-id="646d7-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="646d7-269">Em um navegador, registre um novo usuário (por exemplo, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="646d7-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="646d7-270">Entrar para cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="646d7-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="646d7-271">Verifique se as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="646d7-271">Verify the following operations:</span></span>

* <span data-ttu-id="646d7-272">Usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="646d7-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="646d7-273">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="646d7-274">Os gerentes podem Aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="646d7-275">A exibição `Details` mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="646d7-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="646d7-276">Os administradores podem Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="646d7-277">Usuário</span><span class="sxs-lookup"><span data-stu-id="646d7-277">User</span></span>                | <span data-ttu-id="646d7-278">Propagada pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="646d7-278">Seeded by the app</span></span> | <span data-ttu-id="646d7-279">Opções</span><span class="sxs-lookup"><span data-stu-id="646d7-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="646d7-280">Não</span><span class="sxs-lookup"><span data-stu-id="646d7-280">No</span></span>                | <span data-ttu-id="646d7-281">Editar/Excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="646d7-282">Sim</span><span class="sxs-lookup"><span data-stu-id="646d7-282">Yes</span></span>               | <span data-ttu-id="646d7-283">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="646d7-284">Sim</span><span class="sxs-lookup"><span data-stu-id="646d7-284">Yes</span></span>               | <span data-ttu-id="646d7-285">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="646d7-286">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="646d7-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="646d7-287">Copie a URL para excluir e editar a partir do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="646d7-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="646d7-288">Cole esses links no navegador do usuário de teste para verificar se que o usuário de teste não é possível executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="646d7-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="646d7-289">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="646d7-289">Create the starter app</span></span>

* <span data-ttu-id="646d7-290">Criar um aplicativo páginas Razor denominado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="646d7-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="646d7-291">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="646d7-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="646d7-292">O nome "ContactManager" para o namespace coincida com o namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="646d7-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="646d7-293">`-uld` especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="646d7-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="646d7-294">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="646d7-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="646d7-295">Scaffold o modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="646d7-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="646d7-296">Criar a migração inicial e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="646d7-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="646d7-297">Se você tiver um bug com o comando `dotnet aspnet-codegenerator razorpage`, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="646d7-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="646d7-298">Atualize a âncora **ContactManager** no arquivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="646d7-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="646d7-299">Testar o aplicativo criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="646d7-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="646d7-300">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="646d7-300">Seed the database</span></span>

<span data-ttu-id="646d7-301">Adicione a classe [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :</span><span class="sxs-lookup"><span data-stu-id="646d7-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="646d7-302">Chamar `SeedData.Initialize` de `Main`:</span><span class="sxs-lookup"><span data-stu-id="646d7-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="646d7-303">Se o aplicativo propagado o banco de dados de teste.</span><span class="sxs-lookup"><span data-stu-id="646d7-303">Test that the app seeded the database.</span></span> <span data-ttu-id="646d7-304">Se não houver nenhuma linha no banco de dados de contato, o método de propagação não será executado.</span><span class="sxs-lookup"><span data-stu-id="646d7-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="646d7-305">Este tutorial mostra como criar um aplicativo web do ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="646d7-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="646d7-306">Ele exibe uma lista de contatos criada por usuários autenticados (registrados).</span><span class="sxs-lookup"><span data-stu-id="646d7-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="646d7-307">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="646d7-307">There are three security groups:</span></span>

* <span data-ttu-id="646d7-308">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="646d7-309">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="646d7-310">Apenas os contatos aprovados são visíveis aos usuários.</span><span class="sxs-lookup"><span data-stu-id="646d7-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="646d7-311">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="646d7-312">Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado.</span><span class="sxs-lookup"><span data-stu-id="646d7-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="646d7-313">Rick só pode exibir contatos aprovados e **editar**/**excluir**/**criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="646d7-314">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="646d7-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="646d7-315">Outros usuários não verão o último registro até que um gerente ou administrador altere o status para "Aprovado".</span><span class="sxs-lookup"><span data-stu-id="646d7-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="646d7-317">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="646d7-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

<span data-ttu-id="646d7-319">A imagem a seguir mostra a tela de exibição de detalhes de um contato dos gerentes:</span><span class="sxs-lookup"><span data-stu-id="646d7-319">The following image shows the managers details view of a contact:</span></span>

![Modo de exibição do Gerenciador de um contato](secure-data/_static/manager.png)

<span data-ttu-id="646d7-321">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="646d7-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="646d7-322">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="646d7-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

<span data-ttu-id="646d7-324">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="646d7-324">The administrator has all privileges.</span></span> <span data-ttu-id="646d7-325">Ele pode ler/editar/excluir todos os contatos e alterar os status deles.</span><span class="sxs-lookup"><span data-stu-id="646d7-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="646d7-326">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="646d7-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="646d7-327">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="646d7-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="646d7-328">`ContactIsOwnerAuthorizationHandler`: garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="646d7-329">`ContactManagerAuthorizationHandler`: permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="646d7-330">`ContactAdministratorsAuthorizationHandler`: permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="646d7-331">{1&gt;Pré-requisitos&lt;1}</span><span class="sxs-lookup"><span data-stu-id="646d7-331">Prerequisites</span></span>

<span data-ttu-id="646d7-332">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="646d7-332">This tutorial is advanced.</span></span> <span data-ttu-id="646d7-333">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="646d7-333">You should be familiar with:</span></span>

* [<span data-ttu-id="646d7-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="646d7-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="646d7-335">Autenticação</span><span class="sxs-lookup"><span data-stu-id="646d7-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="646d7-336">Confirmação de conta e de recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="646d7-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="646d7-337">Autorização</span><span class="sxs-lookup"><span data-stu-id="646d7-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="646d7-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="646d7-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="646d7-339">O aplicativo inicial e o concluído</span><span class="sxs-lookup"><span data-stu-id="646d7-339">The starter and completed app</span></span>

<span data-ttu-id="646d7-340">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="646d7-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="646d7-341">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="646d7-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="646d7-342">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="646d7-342">The starter app</span></span>

<span data-ttu-id="646d7-343">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="646d7-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="646d7-344">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="646d7-345">Proteger os dados de usuário</span><span class="sxs-lookup"><span data-stu-id="646d7-345">Secure user data</span></span>

<span data-ttu-id="646d7-346">As seções a seguir têm todas as principais etapas para criar um aplicativo com dados de usuários seguros.</span><span class="sxs-lookup"><span data-stu-id="646d7-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="646d7-347">Talvez seja útil para fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="646d7-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="646d7-348">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="646d7-348">Tie the contact data to the user</span></span>

<span data-ttu-id="646d7-349">Use a ID de usuário de [identidade](xref:security/authentication/identity) ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="646d7-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="646d7-350">Adicione `OwnerID` e `ContactStatus` ao modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="646d7-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="646d7-351">`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="646d7-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="646d7-352">O campo `Status` determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="646d7-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="646d7-353">Criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="646d7-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="646d7-354">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="646d7-354">Add Role services to Identity</span></span>

<span data-ttu-id="646d7-355">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="646d7-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="646d7-356">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="646d7-356">Require authenticated users</span></span>

<span data-ttu-id="646d7-357">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="646d7-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="646d7-358">Você pode recusar a autenticação na página do Razor, no controlador ou no nível do método de ação com o atributo `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="646d7-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="646d7-359">Configurar a política de autenticação padrão para exigir que os usuários sejam autenticados protege recém-adicionado páginas do Razor e controladores.</span><span class="sxs-lookup"><span data-stu-id="646d7-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="646d7-360">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor Pages incluir o atributo `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="646d7-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="646d7-361">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="646d7-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="646d7-362">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="646d7-362">Configure the test account</span></span>

<span data-ttu-id="646d7-363">A classe `SeedData` cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="646d7-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="646d7-364">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="646d7-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="646d7-365">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="646d7-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="646d7-366">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="646d7-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="646d7-367">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="646d7-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="646d7-368">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="646d7-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="646d7-369">Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="646d7-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="646d7-370">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="646d7-371">Torne um dos contatos "Enviado" e um "Rejeitado".</span><span class="sxs-lookup"><span data-stu-id="646d7-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="646d7-372">Adicione a ID de usuário e o status para todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="646d7-373">Somente um contato é exibido:</span><span class="sxs-lookup"><span data-stu-id="646d7-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="646d7-374">Criar o proprietário, manager e manipuladores de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="646d7-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="646d7-375">Crie uma pasta de *autorização* e crie uma classe de `ContactIsOwnerAuthorizationHandler` nela.</span><span class="sxs-lookup"><span data-stu-id="646d7-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="646d7-376">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="646d7-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="646d7-377">O `ContactIsOwnerAuthorizationHandler` o [contexto de chamadas. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="646d7-378">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="646d7-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="646d7-379">Retorne `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="646d7-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="646d7-380">Retorne `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="646d7-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="646d7-381">`Task.CompletedTask` não é êxito ou falha&mdash;ele permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="646d7-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="646d7-382">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="646d7-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="646d7-383">O aplicativo permite que proprietários de contato possam editar/excluir/criar seus dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="646d7-384">`ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="646d7-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="646d7-385">Criar um manipulador de autorização do Gerenciador</span><span class="sxs-lookup"><span data-stu-id="646d7-385">Create a manager authorization handler</span></span>

<span data-ttu-id="646d7-386">Crie uma classe de `ContactManagerAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="646d7-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="646d7-387">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="646d7-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="646d7-388">Somente gerentes possam aprovar ou rejeitar as alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="646d7-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="646d7-389">Crie um manipulador de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="646d7-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="646d7-390">Crie uma classe de `ContactAdministratorsAuthorizationHandler` na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="646d7-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="646d7-391">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="646d7-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="646d7-392">Administrador pode fazer todas as operações.</span><span class="sxs-lookup"><span data-stu-id="646d7-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="646d7-393">O registro dos manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="646d7-393">Register the authorization handlers</span></span>

<span data-ttu-id="646d7-394">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="646d7-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="646d7-395">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="646d7-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="646d7-396">Registre os manipuladores com a coleção de serviços para que eles fiquem disponíveis para a `ContactsController` por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="646d7-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="646d7-397">Adicione o seguinte código ao final do `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="646d7-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="646d7-398">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="646d7-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="646d7-399">Eles são singletons porque não usam o EF e todas as informações necessárias estão no parâmetro `Context` do método `HandleRequirementAsync`.</span><span class="sxs-lookup"><span data-stu-id="646d7-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="646d7-400">Suporte à autorização</span><span class="sxs-lookup"><span data-stu-id="646d7-400">Support authorization</span></span>

<span data-ttu-id="646d7-401">Nesta seção, você atualize as páginas Razor e adicione uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="646d7-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="646d7-402">Examine a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="646d7-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="646d7-403">Examine a classe `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="646d7-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="646d7-404">Essa classe contém os requisitos de aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="646d7-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="646d7-405">Criar uma classe base para as páginas do Razor de contatos</span><span class="sxs-lookup"><span data-stu-id="646d7-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="646d7-406">Crie uma classe base que contém os serviços usados em contatos páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="646d7-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="646d7-407">A classe base coloca o código de inicialização em um único local:</span><span class="sxs-lookup"><span data-stu-id="646d7-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="646d7-408">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="646d7-408">The preceding code:</span></span>

* <span data-ttu-id="646d7-409">Adiciona o serviço de `IAuthorizationService` para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="646d7-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="646d7-410">Adiciona o serviço de `UserManager` de identidade.</span><span class="sxs-lookup"><span data-stu-id="646d7-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="646d7-411">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="646d7-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="646d7-412">Atualizar o CreateModel</span><span class="sxs-lookup"><span data-stu-id="646d7-412">Update the CreateModel</span></span>

<span data-ttu-id="646d7-413">Atualize o Construtor criar modelo de página para usar a classe base `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="646d7-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="646d7-414">Atualize o método `CreateModel.OnPostAsync` para:</span><span class="sxs-lookup"><span data-stu-id="646d7-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="646d7-415">Adicione a ID de usuário ao modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="646d7-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="646d7-416">Chame o manipulador de autorização para verificar se que o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="646d7-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="646d7-417">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="646d7-417">Update the IndexModel</span></span>

<span data-ttu-id="646d7-418">Atualize o método `OnGetAsync` para que somente contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="646d7-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="646d7-419">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="646d7-419">Update the EditModel</span></span>

<span data-ttu-id="646d7-420">Adicione um manipulador de autorização para verificar se que o usuário é proprietária do contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="646d7-421">Como a autorização de recursos está sendo validada, o atributo `[Authorize]` não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="646d7-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="646d7-422">O aplicativo não tiver acesso ao recurso quando atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="646d7-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="646d7-423">Autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="646d7-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="646d7-424">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, carregá-los no modelo de página ou carregá-los dentro do manipulador em si.</span><span class="sxs-lookup"><span data-stu-id="646d7-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="646d7-425">Com frequência, você acessa o recurso, passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="646d7-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="646d7-426">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="646d7-426">Update the DeleteModel</span></span>

<span data-ttu-id="646d7-427">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se que o usuário tem permissão de exclusão no contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="646d7-428">Injetar o serviço de autorização em exibições</span><span class="sxs-lookup"><span data-stu-id="646d7-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="646d7-429">Atualmente, mostra a interface do usuário a edita e excluir links para os contatos, que o usuário não é possível modificar.</span><span class="sxs-lookup"><span data-stu-id="646d7-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="646d7-430">Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="646d7-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="646d7-431">A marcação anterior adiciona várias instruções `using`.</span><span class="sxs-lookup"><span data-stu-id="646d7-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="646d7-432">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="646d7-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="646d7-433">Ocultar links de usuários que não tem permissão para alterar os dados não proteger o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="646d7-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="646d7-434">Ocultar links torna o aplicativo mais fácil de usar, exibindo links só é válidas.</span><span class="sxs-lookup"><span data-stu-id="646d7-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="646d7-435">Os usuários podem hack gerados URLs para invocar editar e excluir operações em dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="646d7-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="646d7-436">A página do Razor ou controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="646d7-437">Detalhes da atualização</span><span class="sxs-lookup"><span data-stu-id="646d7-437">Update Details</span></span>

<span data-ttu-id="646d7-438">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="646d7-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="646d7-439">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="646d7-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="646d7-440">Adicionar ou remover um usuário a uma função</span><span class="sxs-lookup"><span data-stu-id="646d7-440">Add or remove a user to a role</span></span>

<span data-ttu-id="646d7-441">Consulte [este problema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="646d7-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="646d7-442">Remoção de privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="646d7-442">Removing privileges from a user.</span></span> <span data-ttu-id="646d7-443">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="646d7-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="646d7-444">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="646d7-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="646d7-445">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="646d7-445">Test the completed app</span></span>

<span data-ttu-id="646d7-446">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="646d7-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="646d7-447">Escolha uma senha forte: Use oito ou mais caracteres e pelo menos um caractere maiusculo, número e símbolo.</span><span class="sxs-lookup"><span data-stu-id="646d7-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="646d7-448">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="646d7-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="646d7-449">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="646d7-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="646d7-450">Remover e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="646d7-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="646d7-451">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="646d7-452">Uma maneira fácil de testar o aplicativo concluído é iniciar três diferentes navegadores (ou incógnita/InPrivate sessões).</span><span class="sxs-lookup"><span data-stu-id="646d7-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="646d7-453">Em um navegador, registre um novo usuário (por exemplo, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="646d7-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="646d7-454">Entrar para cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="646d7-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="646d7-455">Verifique se as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="646d7-455">Verify the following operations:</span></span>

* <span data-ttu-id="646d7-456">Usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="646d7-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="646d7-457">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="646d7-458">Os gerentes podem Aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="646d7-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="646d7-459">A exibição `Details` mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="646d7-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="646d7-460">Os administradores podem Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="646d7-461">Usuário</span><span class="sxs-lookup"><span data-stu-id="646d7-461">User</span></span>                | <span data-ttu-id="646d7-462">Propagada pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="646d7-462">Seeded by the app</span></span> | <span data-ttu-id="646d7-463">Opções</span><span class="sxs-lookup"><span data-stu-id="646d7-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="646d7-464">Não</span><span class="sxs-lookup"><span data-stu-id="646d7-464">No</span></span>                | <span data-ttu-id="646d7-465">Editar/Excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="646d7-466">Sim</span><span class="sxs-lookup"><span data-stu-id="646d7-466">Yes</span></span>               | <span data-ttu-id="646d7-467">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="646d7-468">Sim</span><span class="sxs-lookup"><span data-stu-id="646d7-468">Yes</span></span>               | <span data-ttu-id="646d7-469">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="646d7-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="646d7-470">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="646d7-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="646d7-471">Copie a URL para excluir e editar a partir do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="646d7-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="646d7-472">Cole esses links no navegador do usuário de teste para verificar se que o usuário de teste não é possível executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="646d7-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="646d7-473">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="646d7-473">Create the starter app</span></span>

* <span data-ttu-id="646d7-474">Criar um aplicativo páginas Razor denominado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="646d7-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="646d7-475">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="646d7-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="646d7-476">O nome "ContactManager" para o namespace coincida com o namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="646d7-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="646d7-477">`-uld` especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="646d7-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="646d7-478">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="646d7-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="646d7-479">Scaffold o modelo de `Contact`.</span><span class="sxs-lookup"><span data-stu-id="646d7-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="646d7-480">Criar a migração inicial e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="646d7-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="646d7-481">Atualize a âncora **ContactManager** no arquivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="646d7-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="646d7-482">Testar o aplicativo criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="646d7-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="646d7-483">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="646d7-483">Seed the database</span></span>

<span data-ttu-id="646d7-484">Adicione a classe [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .</span><span class="sxs-lookup"><span data-stu-id="646d7-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="646d7-485">Chamar `SeedData.Initialize` de `Main`:</span><span class="sxs-lookup"><span data-stu-id="646d7-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="646d7-486">Se o aplicativo propagado o banco de dados de teste.</span><span class="sxs-lookup"><span data-stu-id="646d7-486">Test that the app seeded the database.</span></span> <span data-ttu-id="646d7-487">Se não houver nenhuma linha no banco de dados de contato, o método de propagação não será executado.</span><span class="sxs-lookup"><span data-stu-id="646d7-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="646d7-488">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="646d7-488">Additional resources</span></span>

* [<span data-ttu-id="646d7-489">Criar um aplicativo Web .NET Core e do banco de dados SQL no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="646d7-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="646d7-490">[ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="646d7-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="646d7-491">Este laboratório apresenta mais detalhes sobre os recursos de segurança introduzidos neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="646d7-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="646d7-492">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="646d7-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
