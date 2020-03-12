---
title: Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com a biblioteca de autenticação
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: f9cc2884dcd94c729c45a056ae4327a2c75d34be
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083751"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="17926-102">Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com a biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="17926-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="17926-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="17926-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="17926-104">Para criar um aplicativo autônomo Webassembly Blazor que usa `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="17926-104">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="17926-105">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="17926-105">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="17926-106">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="17926-106">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="17926-107">No Visual Studio, [crie um aplicativo Webassembly Blazor](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="17926-107">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="17926-108">Defina a **autenticação** para **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="17926-108">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="17926-109">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="17926-109">Authentication package</span></span>

<span data-ttu-id="17926-110">Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote para o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17926-110">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="17926-111">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="17926-111">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="17926-112">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="17926-112">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="17926-113">Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="17926-113">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="17926-114">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="17926-114">Authentication service support</span></span>

<span data-ttu-id="17926-115">O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddOidcAuthentication` fornecido pelo pacote `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="17926-115">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="17926-116">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="17926-116">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="17926-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="17926-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="17926-118">O suporte de autenticação para aplicativos autônomos é oferecido usando o Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="17926-118">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="17926-119">O método `AddOidcAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando OIDC.</span><span class="sxs-lookup"><span data-stu-id="17926-119">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="17926-120">Os valores necessários para configurar o aplicativo podem ser obtidos do IP, como Google, Microsoft ou outro provedor compatível com OIDC.</span><span class="sxs-lookup"><span data-stu-id="17926-120">The values required for configuring the app can be obtained from the IP, such as Google, Microsoft, or other OIDC-compliant provider.</span></span> <span data-ttu-id="17926-121">Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.</span><span class="sxs-lookup"><span data-stu-id="17926-121">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="index-page"></a><span data-ttu-id="17926-122">Página de índice</span><span class="sxs-lookup"><span data-stu-id="17926-122">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="17926-123">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="17926-123">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="17926-124">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="17926-124">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="17926-125">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="17926-125">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="17926-126">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="17926-126">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
