---
title: Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização
author: rick-anderson
description: Saiba como criar um aplicativo páginas Razor com dados protegidos por autorização do usuário. Inclui HTTPS, autenticação, segurança, identidade do ASP.NET Core.
ms.author: riande
ms.date: 12/18/2018
ms.custom: seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: fa82d3d99f4e4b7ad17ed385fb7c029745797e8d
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637827"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="e7a20-104">Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="e7a20-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="e7a20-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="e7a20-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="e7a20-106">Ver [esse PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="e7a20-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="e7a20-107">A versão 1.1 do ASP.NET Core deste tutorial está [nesta](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data) pasta.</span><span class="sxs-lookup"><span data-stu-id="e7a20-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="e7a20-108">O exemplo do ASP.NET Core 1.1 está em [exemplos](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="e7a20-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e7a20-109">Consulte [esse pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="e7a20-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e7a20-110">Este tutorial mostra como criar um aplicativo web do ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="e7a20-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="e7a20-111">Ele exibe uma lista de contatos criada por usuários autenticados (registrados).</span><span class="sxs-lookup"><span data-stu-id="e7a20-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="e7a20-112">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="e7a20-112">There are three security groups:</span></span>

* <span data-ttu-id="e7a20-113">**Usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="e7a20-114">**Gerenciadores de** podem aprovar ou rejeitar dados de um contato.</span><span class="sxs-lookup"><span data-stu-id="e7a20-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="e7a20-115">Apenas os contatos aprovados são visíveis aos usuários.</span><span class="sxs-lookup"><span data-stu-id="e7a20-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="e7a20-116">**Os administradores** podem rejeitar/aprovar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="e7a20-117">Na imagem a seguir, o usuário Rick (`rick@example.com`) está conectado.</span><span class="sxs-lookup"><span data-stu-id="e7a20-117">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="e7a20-118">Rick só pode ver os contatos aprovados e os links **editar**/**excluir**/**criar novo** de seus contatos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-118">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="e7a20-119">Somente o último registro criado por Rick exibe os links **editar** e **excluir**.</span><span class="sxs-lookup"><span data-stu-id="e7a20-119">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="e7a20-120">Outros usuários não verão o último registro até que um gerente ou administrador altere o status para "Aprovado".</span><span class="sxs-lookup"><span data-stu-id="e7a20-120">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="e7a20-122">Na imagem a seguir, `manager@contoso.com` está conectado e na função de gerenciadores:</span><span class="sxs-lookup"><span data-stu-id="e7a20-122">In the following image, `manager@contoso.com` is signed in and in the managers role:</span></span>

![Captura de tela mostrando manager@contoso.com conectado](secure-data/_static/manager1.png)

<span data-ttu-id="e7a20-124">A imagem a seguir mostra a tela de exibição de detalhes de um contato dos gerentes:</span><span class="sxs-lookup"><span data-stu-id="e7a20-124">The following image shows the managers details view of a contact:</span></span>

![Modo de exibição do Gerenciador de um contato](secure-data/_static/manager.png)

<span data-ttu-id="e7a20-126">O botões **aprovar** e **rejeitar** são exibidos somente para administradores e gerentes.</span><span class="sxs-lookup"><span data-stu-id="e7a20-126">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="e7a20-127">Na imagem a seguir, `admin@contoso.com` está conectado e na função de gerenciadores:</span><span class="sxs-lookup"><span data-stu-id="e7a20-127">In the following image, `admin@contoso.com` is signed in and in the administrators role:</span></span>

![Captura de tela mostrando admin@contoso.com conectado](secure-data/_static/admin.png)

<span data-ttu-id="e7a20-129">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="e7a20-129">The administrator has all privileges.</span></span> <span data-ttu-id="e7a20-130">Ele pode ler/editar/excluir todos os contatos e alterar os status deles.</span><span class="sxs-lookup"><span data-stu-id="e7a20-130">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="e7a20-131">O aplicativo foi criado fazendo [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) do seguinte modelo de `Contact`:</span><span class="sxs-lookup"><span data-stu-id="e7a20-131">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet)]

<span data-ttu-id="e7a20-132">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="e7a20-132">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="e7a20-133">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só pode editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-133">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="e7a20-134">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovar ou rejeitar os contatos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-134">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="e7a20-135">`ContactAdministratorsAuthorizationHandler`: Permite aos administradores para aprovar ou rejeitar os contatos e editar/excluir contatos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-135">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e7a20-136">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e7a20-136">Prerequisites</span></span>

<span data-ttu-id="e7a20-137">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="e7a20-137">This tutorial is advanced.</span></span> <span data-ttu-id="e7a20-138">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="e7a20-138">You should be familiar with:</span></span>

* [<span data-ttu-id="e7a20-139">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7a20-139">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="e7a20-140">Autenticação</span><span class="sxs-lookup"><span data-stu-id="e7a20-140">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="e7a20-141">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="e7a20-141">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="e7a20-142">Autorização</span><span class="sxs-lookup"><span data-stu-id="e7a20-142">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="e7a20-143">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e7a20-143">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="e7a20-144">No ASP.NET Core 2.1, `User.IsInRole` Falha ao usar `AddDefaultIdentity`.</span><span class="sxs-lookup"><span data-stu-id="e7a20-144">In ASP.NET Core 2.1, `User.IsInRole` fails when using `AddDefaultIdentity`.</span></span> <span data-ttu-id="e7a20-145">Este tutorial usa `AddDefaultIdentity` e, portanto, exige o ASP.NET Core 2.2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="e7a20-145">This tutorial uses `AddDefaultIdentity` and therefore requires ASP.NET Core 2.2 or later.</span></span> <span data-ttu-id="e7a20-146">Ver [esse problema de GitHub](https://github.com/aspnet/Identity/issues/1813#issuecomment-394543909) para uma solução alternativa.</span><span class="sxs-lookup"><span data-stu-id="e7a20-146">See [this GitHub issue](https://github.com/aspnet/Identity/issues/1813#issuecomment-394543909) for a work-around.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="e7a20-147">O aplicativo inicial e o concluído</span><span class="sxs-lookup"><span data-stu-id="e7a20-147">The starter and completed app</span></span>

<span data-ttu-id="e7a20-148">[Baixe](xref:index#how-to-download-a-sample) as [concluída](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2) aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-148">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2) app.</span></span> <span data-ttu-id="e7a20-149">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="e7a20-149">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="e7a20-150">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="e7a20-150">The starter app</span></span>

<span data-ttu-id="e7a20-151">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2).</span><span class="sxs-lookup"><span data-stu-id="e7a20-151">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2) app.</span></span>

<span data-ttu-id="e7a20-152">Execute o aplicativo, clique em **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="e7a20-152">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="e7a20-153">Proteger os dados de usuário</span><span class="sxs-lookup"><span data-stu-id="e7a20-153">Secure user data</span></span>

<span data-ttu-id="e7a20-154">As seções a seguir têm todas as principais etapas para criar um aplicativo com dados de usuários seguros.</span><span class="sxs-lookup"><span data-stu-id="e7a20-154">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="e7a20-155">Talvez seja útil para fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="e7a20-155">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="e7a20-156">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="e7a20-156">Tie the contact data to the user</span></span>

<span data-ttu-id="e7a20-157">Usar o ASP.NET [identidade](xref:security/authentication/identity) ID de usuário para garantir que os usuários pode editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="e7a20-157">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="e7a20-158">Adicione `OwnerID` e `ContactStatus` para o `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="e7a20-158">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="e7a20-159">`OwnerID` é a ID do usuário da tabela `AspNetUser` no banco de dados de [identidade](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="e7a20-159">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="e7a20-160">O campo `Status` determina se um contato pode ser visto por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="e7a20-160">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="e7a20-161">Criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e7a20-161">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="e7a20-162">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="e7a20-162">Add Role services to Identity</span></span>

<span data-ttu-id="e7a20-163">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="e7a20-163">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="e7a20-164">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="e7a20-164">Require authenticated users</span></span>

<span data-ttu-id="e7a20-165">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="e7a20-165">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="e7a20-166">Você pode recusar a autenticação em nível de método de ação, controlador ou página Razor com o `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-166">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="e7a20-167">Configurar a política de autenticação padrão para exigir que os usuários sejam autenticados protege recém-adicionado páginas do Razor e controladores.</span><span class="sxs-lookup"><span data-stu-id="e7a20-167">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="e7a20-168">Autenticação necessária por padrão é mais segura do que contar com novos controladores e páginas do Razor para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-168">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="e7a20-169">Adicionar [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)às páginas Índice, Sobre e Contatos para que usuários anônimos possam obter informações sobre o site antes de eles se registrarem.</span><span class="sxs-lookup"><span data-stu-id="e7a20-169">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="e7a20-170">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="e7a20-170">Configure the test account</span></span>

<span data-ttu-id="e7a20-171">A classe `SeedData` cria duas contas: administrador e gerenciador.</span><span class="sxs-lookup"><span data-stu-id="e7a20-171">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="e7a20-172">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="e7a20-172">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="e7a20-173">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7a20-173">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="e7a20-174">Se uma senha forte não for especificada, uma exceção é gerada quando `SeedData.Initialize` é chamado.</span><span class="sxs-lookup"><span data-stu-id="e7a20-174">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="e7a20-175">Atualização `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="e7a20-175">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="e7a20-176">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="e7a20-176">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="e7a20-177">Atualize o método `Initialize` na classe `SeedData` para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="e7a20-177">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="e7a20-178">Adicione a ID de usuário de administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-178">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="e7a20-179">Torne um dos contatos "Enviado" e um "Rejeitado".</span><span class="sxs-lookup"><span data-stu-id="e7a20-179">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="e7a20-180">Adicione a ID de usuário e o status para todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-180">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="e7a20-181">Somente um contato é exibido:</span><span class="sxs-lookup"><span data-stu-id="e7a20-181">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="e7a20-182">Criar o proprietário, manager e manipuladores de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="e7a20-182">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="e7a20-183">Criar uma classe `ContactIsOwnerAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="e7a20-183">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7a20-184">O `ContactIsOwnerAuthorizationHandler` verifica que o usuário que atua em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="e7a20-184">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="e7a20-185">O `ContactIsOwnerAuthorizationHandler` chamadas [contexto. Êxito](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="e7a20-185">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="e7a20-186">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="e7a20-186">Authorization handlers generally:</span></span>

* <span data-ttu-id="e7a20-187">Retornar `context.Succeed` quando os requisitos sejam atendidos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-187">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="e7a20-188">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-188">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="e7a20-189">`Task.CompletedTask` não é sucesso ou falha&mdash;permite que outros manipuladores de autorização executar.</span><span class="sxs-lookup"><span data-stu-id="e7a20-189">`Task.CompletedTask` is neither success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="e7a20-190">Se você precisar fazer explicitamente, retornar [contexto. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="e7a20-190">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="e7a20-191">O aplicativo permite que proprietários de contato possam editar/excluir/criar seus dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-191">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="e7a20-192">`ContactIsOwnerAuthorizationHandler` não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="e7a20-192">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="e7a20-193">Criar um manipulador de autorização do Gerenciador</span><span class="sxs-lookup"><span data-stu-id="e7a20-193">Create a manager authorization handler</span></span>

<span data-ttu-id="e7a20-194">Criar uma classe `ContactManagerAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="e7a20-194">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7a20-195">O `ContactManagerAuthorizationHandler` verifica o usuário atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="e7a20-195">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="e7a20-196">Somente gerentes possam aprovar ou rejeitar as alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="e7a20-196">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="e7a20-197">Crie um manipulador de autorização do administrador</span><span class="sxs-lookup"><span data-stu-id="e7a20-197">Create an administrator authorization handler</span></span>

<span data-ttu-id="e7a20-198">Criar uma classe `ContactAdministratorsAuthorizationHandler` na pasta *autorização*.</span><span class="sxs-lookup"><span data-stu-id="e7a20-198">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7a20-199">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="e7a20-199">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="e7a20-200">Administrador pode fazer todas as operações.</span><span class="sxs-lookup"><span data-stu-id="e7a20-200">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="e7a20-201">O registro dos manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="e7a20-201">Register the authorization handlers</span></span>

<span data-ttu-id="e7a20-202">Serviços usando o Entity Framework Core devem ser registrados [injeção de dependência](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="e7a20-202">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="e7a20-203">O `ContactIsOwnerAuthorizationHandler` usa o ASP.NET Core [identidade](xref:security/authentication/identity), que se baseia no Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e7a20-203">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="e7a20-204">O registro dos manipuladores com a coleção de serviço para que eles estejam disponíveis para o `ContactsController` por meio [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e7a20-204">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e7a20-205">Adicione o seguinte código ao final da `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e7a20-205">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="e7a20-206">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="e7a20-206">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="e7a20-207">Eles são singletons porque eles não usam o EF e todas as informações necessárias na `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="e7a20-207">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="e7a20-208">Suporte à autorização</span><span class="sxs-lookup"><span data-stu-id="e7a20-208">Support authorization</span></span>

<span data-ttu-id="e7a20-209">Nesta seção, você atualize as páginas Razor e adicione uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="e7a20-209">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="e7a20-210">Examine a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="e7a20-210">Review the contact operations requirements class</span></span>

<span data-ttu-id="e7a20-211">Examine o `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="e7a20-211">Review the `ContactOperations` class.</span></span> <span data-ttu-id="e7a20-212">Essa classe contém os requisitos de aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="e7a20-212">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="e7a20-213">Criar uma classe base para as páginas do Razor de contatos</span><span class="sxs-lookup"><span data-stu-id="e7a20-213">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="e7a20-214">Crie uma classe base que contém os serviços usados em contatos páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="e7a20-214">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="e7a20-215">A classe base coloca o código de inicialização em um único local:</span><span class="sxs-lookup"><span data-stu-id="e7a20-215">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="e7a20-216">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="e7a20-216">The preceding code:</span></span>

* <span data-ttu-id="e7a20-217">Adiciona o serviço `IAuthorizationService` para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="e7a20-217">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="e7a20-218">Adiciona o serviço de identidade `UserManager`.</span><span class="sxs-lookup"><span data-stu-id="e7a20-218">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="e7a20-219">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="e7a20-219">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="e7a20-220">Atualizar o CreateModel</span><span class="sxs-lookup"><span data-stu-id="e7a20-220">Update the CreateModel</span></span>

<span data-ttu-id="e7a20-221">Atualize o construtor de criar modelo de página para usar a classe base `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="e7a20-221">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="e7a20-222">Atualize o método `CreateModel.OnPostAsync` para:</span><span class="sxs-lookup"><span data-stu-id="e7a20-222">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="e7a20-223">Adicione a ID de usuário ao modelo `Contact`.</span><span class="sxs-lookup"><span data-stu-id="e7a20-223">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="e7a20-224">Chame o manipulador de autorização para verificar se que o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="e7a20-224">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="e7a20-225">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="e7a20-225">Update the IndexModel</span></span>

<span data-ttu-id="e7a20-226">Atualize o método `OnGetAsync` para que apenas os contatos aprovados sejam mostrados aos usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="e7a20-226">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="e7a20-227">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="e7a20-227">Update the EditModel</span></span>

<span data-ttu-id="e7a20-228">Adicione um manipulador de autorização para verificar se que o usuário é proprietária do contato.</span><span class="sxs-lookup"><span data-stu-id="e7a20-228">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="e7a20-229">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="e7a20-229">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="e7a20-230">O aplicativo não tiver acesso ao recurso quando atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-230">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="e7a20-231">Autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="e7a20-231">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="e7a20-232">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, carregá-los no modelo de página ou carregá-los dentro do manipulador em si.</span><span class="sxs-lookup"><span data-stu-id="e7a20-232">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="e7a20-233">Com frequência, você acessa o recurso, passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="e7a20-233">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="e7a20-234">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="e7a20-234">Update the DeleteModel</span></span>

<span data-ttu-id="e7a20-235">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se que o usuário tem permissão de exclusão no contato.</span><span class="sxs-lookup"><span data-stu-id="e7a20-235">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="e7a20-236">Injetar o serviço de autorização em exibições</span><span class="sxs-lookup"><span data-stu-id="e7a20-236">Inject the authorization service into the views</span></span>

<span data-ttu-id="e7a20-237">Atualmente, mostra a interface do usuário a edita e excluir links para os contatos, que o usuário não é possível modificar.</span><span class="sxs-lookup"><span data-stu-id="e7a20-237">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="e7a20-238">Injetar o serviço de autorização no arquivo *Views/_ViewImports.cshtml* para que ele esteja disponível para todos os modos de exibição:</span><span class="sxs-lookup"><span data-stu-id="e7a20-238">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="e7a20-239">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="e7a20-239">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="e7a20-240">Atualizar o **editar** e **excluir** vincula na *Pages/Contacts/Index.cshtml* para que eles sejam renderizados somente para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="e7a20-240">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="e7a20-241">Ocultar links de usuários que não tem permissão para alterar os dados não proteger o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-241">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="e7a20-242">Ocultar links torna o aplicativo mais fácil de usar, exibindo links só é válidas.</span><span class="sxs-lookup"><span data-stu-id="e7a20-242">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="e7a20-243">Os usuários podem hack gerados URLs para invocar editar e excluir operações em dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="e7a20-243">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="e7a20-244">A página do Razor ou controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-244">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="e7a20-245">Detalhes da atualização</span><span class="sxs-lookup"><span data-stu-id="e7a20-245">Update Details</span></span>

<span data-ttu-id="e7a20-246">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="e7a20-246">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="e7a20-247">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="e7a20-247">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="e7a20-248">Adicionar ou remover um usuário a uma função</span><span class="sxs-lookup"><span data-stu-id="e7a20-248">Add or remove a user to a role</span></span>

<span data-ttu-id="e7a20-249">Ver [esse problema](https://github.com/aspnet/Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="e7a20-249">See [this issue](https://github.com/aspnet/Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="e7a20-250">Remoção de privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a20-250">Removing privileges from a user.</span></span> <span data-ttu-id="e7a20-251">Por exemplo um silenciament um usuário em um aplicativo de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-251">For example muting a user in a chat app.</span></span>
* <span data-ttu-id="e7a20-252">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a20-252">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="e7a20-253">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="e7a20-253">Test the completed app</span></span>

<span data-ttu-id="e7a20-254">Se você ainda não tiver configurado uma senha para contas de usuário propagados, use o [ferramenta Secret Manager](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="e7a20-254">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="e7a20-255">Escolha uma senha forte: Usar oito ou mais caracteres e pelo menos um caractere maiusculo, número e símbolo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-255">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="e7a20-256">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="e7a20-256">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="e7a20-257">Execute o seguinte comando na pasta do projeto, onde `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="e7a20-257">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="e7a20-258">Se o aplicativo tem contatos:</span><span class="sxs-lookup"><span data-stu-id="e7a20-258">If the app has contacts:</span></span>

* <span data-ttu-id="e7a20-259">Excluir todos os registros no `Contact` tabela.</span><span class="sxs-lookup"><span data-stu-id="e7a20-259">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="e7a20-260">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-260">Restart the app to seed the database.</span></span>

<span data-ttu-id="e7a20-261">Uma maneira fácil de testar o aplicativo concluído é iniciar três diferentes navegadores (ou incógnita/InPrivate sessões).</span><span class="sxs-lookup"><span data-stu-id="e7a20-261">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="e7a20-262">Em um navegador, registre um novo usuário (por exemplo, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="e7a20-262">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="e7a20-263">Entrar para cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="e7a20-263">Sign in to each browser with a different user.</span></span> <span data-ttu-id="e7a20-264">Verifique se as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="e7a20-264">Verify the following operations:</span></span>

* <span data-ttu-id="e7a20-265">Usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-265">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="e7a20-266">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-266">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="e7a20-267">Os gerentes podem Aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="e7a20-267">Managers can approve/reject contact data.</span></span> <span data-ttu-id="e7a20-268">A tela `Details` mostra os botões **aprovar** e **rejeitar**.</span><span class="sxs-lookup"><span data-stu-id="e7a20-268">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="e7a20-269">Os administradores podem Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-269">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="e7a20-270">User</span><span class="sxs-lookup"><span data-stu-id="e7a20-270">User</span></span>                | <span data-ttu-id="e7a20-271">Propagada pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="e7a20-271">Seeded by the app</span></span> | <span data-ttu-id="e7a20-272">Opções</span><span class="sxs-lookup"><span data-stu-id="e7a20-272">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="e7a20-273">Não</span><span class="sxs-lookup"><span data-stu-id="e7a20-273">No</span></span>                | <span data-ttu-id="e7a20-274">Editar/Excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-274">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="e7a20-275">Sim</span><span class="sxs-lookup"><span data-stu-id="e7a20-275">Yes</span></span>               | <span data-ttu-id="e7a20-276">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-276">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="e7a20-277">Sim</span><span class="sxs-lookup"><span data-stu-id="e7a20-277">Yes</span></span>               | <span data-ttu-id="e7a20-278">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="e7a20-278">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="e7a20-279">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="e7a20-279">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="e7a20-280">Copie a URL para excluir e editar a partir do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="e7a20-280">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="e7a20-281">Cole esses links no navegador do usuário de teste para verificar se que o usuário de teste não é possível executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="e7a20-281">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="e7a20-282">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="e7a20-282">Create the starter app</span></span>

* <span data-ttu-id="e7a20-283">Criar um aplicativo páginas Razor denominado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="e7a20-283">Create a Razor Pages app named "ContactManager"</span></span>
   * <span data-ttu-id="e7a20-284">Criar o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="e7a20-284">Create the app with **Individual User Accounts**.</span></span>
   * <span data-ttu-id="e7a20-285">O nome "ContactManager" para o namespace coincida com o namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-285">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
   * <span data-ttu-id="e7a20-286">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="e7a20-286">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="e7a20-287">Adicione *Models\Contact.cs*:</span><span class="sxs-lookup"><span data-stu-id="e7a20-287">Add *Models\Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="e7a20-288">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="e7a20-288">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="e7a20-289">Criar a migração inicial e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e7a20-289">Create initial migration and update the database:</span></span>

```console
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

* <span data-ttu-id="e7a20-290">Atualizar o link de **ContactManager** no arquivo *Pages/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e7a20-290">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

```cshtml
<a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
```

* <span data-ttu-id="e7a20-291">Testar o aplicativo criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="e7a20-291">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="e7a20-292">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e7a20-292">Seed the database</span></span>

<span data-ttu-id="e7a20-293">Adicione a [SeedData](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) de classe para o *dados* pasta.</span><span class="sxs-lookup"><span data-stu-id="e7a20-293">Add the [SeedData](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="e7a20-294">Chame `SeedData.Initialize` de `Main`:</span><span class="sxs-lookup"><span data-stu-id="e7a20-294">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="e7a20-295">Se o aplicativo propagado o banco de dados de teste.</span><span class="sxs-lookup"><span data-stu-id="e7a20-295">Test that the app seeded the database.</span></span> <span data-ttu-id="e7a20-296">Se não houver nenhuma linha no banco de dados de contato, o método de propagação não será executado.</span><span class="sxs-lookup"><span data-stu-id="e7a20-296">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="e7a20-297">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e7a20-297">Additional resources</span></span>

* [<span data-ttu-id="e7a20-298">Criar um aplicativo web .NET Core e o banco de dados SQL no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e7a20-298">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="e7a20-299">[Laboratório de autorização do ASP.NET Core](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="e7a20-299">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="e7a20-300">Este laboratório apresenta mais detalhes sobre os recursos de segurança introduzidos neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7a20-300">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="e7a20-301">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="e7a20-301">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

::: moniker-end
