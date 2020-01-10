---
title: Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais
author: rick-anderson
description: Descubra artigos com base em projetos ASP.NET Core criados com contas de usuário individuais.
ms.author: riande
ms.date: 12/11/2019
uid: security/authentication/individual
ms.openlocfilehash: 7ef0d5eabded61d04fb9fe7be384a663ad7ea5f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828705"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="d06ac-103">Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais</span><span class="sxs-lookup"><span data-stu-id="d06ac-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="d06ac-104">ASP.NET Core identidade está incluída nos modelos de projeto no Visual Studio com a opção "contas de usuário individuais".</span><span class="sxs-lookup"><span data-stu-id="d06ac-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="d06ac-105">Os modelos de autenticação estão disponíveis em CLI do .NET Core com `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="d06ac-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="d06ac-106">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5833) para autenticação da API Web.</span><span class="sxs-lookup"><span data-stu-id="d06ac-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="d06ac-107">Sem Autenticação</span><span class="sxs-lookup"><span data-stu-id="d06ac-107">No Authentication</span></span>

<span data-ttu-id="d06ac-108">A autenticação é especificada no CLI do .NET Core com a opção `-au`.</span><span class="sxs-lookup"><span data-stu-id="d06ac-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="d06ac-109">No Visual Studio, a caixa de diálogo **alterar autenticação** está disponível para novos aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="d06ac-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="d06ac-110">O padrão para novos aplicativos Web no Visual Studio **não é autenticação**.</span><span class="sxs-lookup"><span data-stu-id="d06ac-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="d06ac-111">Projetos criados sem autenticação:</span><span class="sxs-lookup"><span data-stu-id="d06ac-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="d06ac-112">Não conte páginas da Web e interface do usuário para entrar e sair.</span><span class="sxs-lookup"><span data-stu-id="d06ac-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="d06ac-113">Não contêm código de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d06ac-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="d06ac-114">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="d06ac-114">Windows Authentication</span></span>

<span data-ttu-id="d06ac-115">A autenticação do Windows é especificada para novos aplicativos Web no CLI do .NET Core com a opção `-au Windows`.</span><span class="sxs-lookup"><span data-stu-id="d06ac-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="d06ac-116">No Visual Studio, a caixa de diálogo **alterar autenticação** fornece as opções de **autenticação do Windows** .</span><span class="sxs-lookup"><span data-stu-id="d06ac-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="d06ac-117">Se a autenticação do Windows for selecionada, o aplicativo será configurado para usar o [módulo IIS de autenticação do Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="d06ac-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="d06ac-118">A autenticação do Windows destina-se a sites da intranet.</span><span class="sxs-lookup"><span data-stu-id="d06ac-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="d06ac-119">novas opções de autenticação de webapp do dotnet</span><span class="sxs-lookup"><span data-stu-id="d06ac-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="d06ac-120">A tabela a seguir mostra as opções de autenticação disponíveis para novos aplicativos Web:</span><span class="sxs-lookup"><span data-stu-id="d06ac-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="d06ac-121">Opção</span><span class="sxs-lookup"><span data-stu-id="d06ac-121">Option</span></span> | <span data-ttu-id="d06ac-122">Tipo de autenticação</span><span class="sxs-lookup"><span data-stu-id="d06ac-122">Type of authentication</span></span> | <span data-ttu-id="d06ac-123">Link para mais informações</span><span class="sxs-lookup"><span data-stu-id="d06ac-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="d06ac-124">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d06ac-124">None</span></span>            |  <span data-ttu-id="d06ac-125">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="d06ac-125">No authentication</span></span> | | 
| <span data-ttu-id="d06ac-126">Individual</span><span class="sxs-lookup"><span data-stu-id="d06ac-126">Individual</span></span>      |  <span data-ttu-id="d06ac-127">Autenticação individual</span><span class="sxs-lookup"><span data-stu-id="d06ac-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="d06ac-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="d06ac-128">IndividualB2C</span></span>   |  <span data-ttu-id="d06ac-129">Autenticação individual hospedada na nuvem com o Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d06ac-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="d06ac-130">B2C do AD do Azure</span><span class="sxs-lookup"><span data-stu-id="d06ac-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="d06ac-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="d06ac-131">SingleOrg</span></span>       |  <span data-ttu-id="d06ac-132">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="d06ac-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="d06ac-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d06ac-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d06ac-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="d06ac-134">MultiOrg</span></span>        |  <span data-ttu-id="d06ac-135">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="d06ac-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="d06ac-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d06ac-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d06ac-137">Portal</span><span class="sxs-lookup"><span data-stu-id="d06ac-137">Windows</span></span>         |  <span data-ttu-id="d06ac-138">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="d06ac-138">Windows authentication</span></span> | [<span data-ttu-id="d06ac-139">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="d06ac-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="d06ac-140">Novas opções de autenticação webapp do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06ac-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="d06ac-141">A tabela a seguir mostra as opções de autenticação disponíveis ao criar um novo aplicativo Web com o Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d06ac-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="d06ac-142">Opção</span><span class="sxs-lookup"><span data-stu-id="d06ac-142">Option</span></span> | <span data-ttu-id="d06ac-143">Tipo de autenticação</span><span class="sxs-lookup"><span data-stu-id="d06ac-143">Type of authentication</span></span> | <span data-ttu-id="d06ac-144">Link para mais informações</span><span class="sxs-lookup"><span data-stu-id="d06ac-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="d06ac-145">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d06ac-145">None</span></span>            |  <span data-ttu-id="d06ac-146">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="d06ac-146">No authentication</span></span> | | 
| <span data-ttu-id="d06ac-147">Contas de usuário individuais/armazenar contas de usuário no aplicativo</span><span class="sxs-lookup"><span data-stu-id="d06ac-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="d06ac-148">Autenticação individual</span><span class="sxs-lookup"><span data-stu-id="d06ac-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="d06ac-149">Contas de usuário individuais/Conecte-se a um armazenamento de usuário existente na nuvem</span><span class="sxs-lookup"><span data-stu-id="d06ac-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="d06ac-150">Autenticação individual hospedada na nuvem com o Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d06ac-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="d06ac-151">B2C do AD do Azure</span><span class="sxs-lookup"><span data-stu-id="d06ac-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="d06ac-152">Nuvem corporativa ou de estudante/organização única</span><span class="sxs-lookup"><span data-stu-id="d06ac-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="d06ac-153">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="d06ac-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="d06ac-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d06ac-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d06ac-155">Nuvem corporativa ou de estudante/várias org</span><span class="sxs-lookup"><span data-stu-id="d06ac-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="d06ac-156">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="d06ac-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="d06ac-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d06ac-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d06ac-158">Portal</span><span class="sxs-lookup"><span data-stu-id="d06ac-158">Windows</span></span>         |  <span data-ttu-id="d06ac-159">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="d06ac-159">Windows authentication</span></span> | [<span data-ttu-id="d06ac-160">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="d06ac-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="d06ac-161">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d06ac-161">Additional resources</span></span>

<span data-ttu-id="d06ac-162">Os artigos a seguir mostram como usar o código gerado em ASP.NET Core modelos que usam contas de usuário individuais:</span><span class="sxs-lookup"><span data-stu-id="d06ac-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="d06ac-163">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d06ac-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d06ac-164">Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="d06ac-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
