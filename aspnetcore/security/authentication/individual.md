---
title: Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais
author: rick-anderson
description: Descubra artigos com base em projetos ASP.NET Core criados com contas de usuário individuais.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080699"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="e17f7-103">Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais</span><span class="sxs-lookup"><span data-stu-id="e17f7-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="e17f7-104">ASP.NET Core identidade está incluída nos modelos de projeto no Visual Studio com a opção "contas de usuário individuais".</span><span class="sxs-lookup"><span data-stu-id="e17f7-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="e17f7-105">Os modelos de autenticação estão disponíveis no CLI do .NET Core `-au Individual`com:</span><span class="sxs-lookup"><span data-stu-id="e17f7-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="e17f7-106">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/5833) para autenticação da API Web.</span><span class="sxs-lookup"><span data-stu-id="e17f7-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="e17f7-107">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="e17f7-107">No Authentication</span></span>

<span data-ttu-id="e17f7-108">A autenticação é especificada no CLI do .NET Core com a `-au` opção.</span><span class="sxs-lookup"><span data-stu-id="e17f7-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="e17f7-109">No Visual Studio, a caixa de diálogo **alterar autenticação** está disponível para novos aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="e17f7-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="e17f7-110">O padrão para novos aplicativos Web no Visual Studio **não é autenticação**.</span><span class="sxs-lookup"><span data-stu-id="e17f7-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="e17f7-111">Projetos criados sem autenticação:</span><span class="sxs-lookup"><span data-stu-id="e17f7-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="e17f7-112">Não conte páginas da Web e interface do usuário para entrar e sair.</span><span class="sxs-lookup"><span data-stu-id="e17f7-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="e17f7-113">Não contêm código de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e17f7-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="e17f7-114">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="e17f7-114">Windows Authentication</span></span>

<span data-ttu-id="e17f7-115">A autenticação do Windows é especificada para novos aplicativos Web no CLI do .NET Core com `-au Windows` a opção.</span><span class="sxs-lookup"><span data-stu-id="e17f7-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="e17f7-116">No Visual Studio, a caixa de diálogo **alterar autenticação** fornece as opções de **autenticação do Windows** .</span><span class="sxs-lookup"><span data-stu-id="e17f7-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="e17f7-117">Se a autenticação do Windows for selecionada, o aplicativo será configurado para usar o [módulo IIS de autenticação do Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="e17f7-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="e17f7-118">A autenticação do Windows destina-se a sites da intranet.</span><span class="sxs-lookup"><span data-stu-id="e17f7-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e17f7-119">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e17f7-119">Additional resources</span></span>

<span data-ttu-id="e17f7-120">Os artigos a seguir mostram como usar o código gerado em ASP.NET Core modelos que usam contas de usuário individuais:</span><span class="sxs-lookup"><span data-stu-id="e17f7-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="e17f7-121">Autenticação de dois fatores com SMS</span><span class="sxs-lookup"><span data-stu-id="e17f7-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="e17f7-122">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e17f7-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="e17f7-123">Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="e17f7-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
