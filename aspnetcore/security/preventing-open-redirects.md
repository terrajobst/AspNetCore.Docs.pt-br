---
title: Impedir ataques de redirecionamento aberto no ASP.NET Core
author: ardalis
description: Mostra como evitar ataques de redirecionamento abertos em relação a um aplicativo ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 0896189d2caaccb19647eb7c6d57f29dfc0290dd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891613"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="f97d4-103">Impedir ataques de redirecionamento aberto no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f97d4-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="f97d4-104">Um aplicativo web que redireciona para uma URL que é especificada por meio de solicitação, como os dados de formulário ou cadeia de consulta potencialmente pode ser violado para redirecionar usuários para uma URL externa e mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="f97d4-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="f97d4-105">Essa violação é chamado de um ataque de redirecionamento aberto.</span><span class="sxs-lookup"><span data-stu-id="f97d4-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="f97d4-106">Sempre que a lógica do aplicativo redireciona para uma URL especificada, verifique se a URL de redirecionamento não foi adulterada.</span><span class="sxs-lookup"><span data-stu-id="f97d4-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="f97d4-107">O ASP.NET Core tem funcionalidade interna para ajudar a proteger aplicativos contra ataques de redirecionamento aberto (também conhecido como open redirecionamento).</span><span class="sxs-lookup"><span data-stu-id="f97d4-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="f97d4-108">O que é um ataque de redirecionamento aberto?</span><span class="sxs-lookup"><span data-stu-id="f97d4-108">What is an open redirect attack?</span></span>

<span data-ttu-id="f97d4-109">Aplicativos Web com frequência redirecionar os usuários para uma página de logon quando acessarem os recursos que exigem a autenticação.</span><span class="sxs-lookup"><span data-stu-id="f97d4-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="f97d4-110">O redirecionamento normalmente inclui uma `returnUrl` parâmetro querystring para que o usuário pode ser retornado para a URL solicitada originalmente depois que eles fizeram logon com êxito.</span><span class="sxs-lookup"><span data-stu-id="f97d4-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="f97d4-111">Depois que o usuário é autenticado, ele serão redirecionados para a URL que eles solicitaram originalmente.</span><span class="sxs-lookup"><span data-stu-id="f97d4-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="f97d4-112">Como a URL de destino é especificada na sequência de consulta da solicitação, um usuário mal-intencionado pode violar a querystring.</span><span class="sxs-lookup"><span data-stu-id="f97d4-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="f97d4-113">Uma cadeia de consulta violada pode permitir que o site redirecionar o usuário para um site externo, mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="f97d4-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="f97d4-114">Essa técnica é chamada de um ataque de redirecionamento (ou redirecionamento) aberto.</span><span class="sxs-lookup"><span data-stu-id="f97d4-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="f97d4-115">Um ataque de exemplo</span><span class="sxs-lookup"><span data-stu-id="f97d4-115">An example attack</span></span>

<span data-ttu-id="f97d4-116">Um usuário mal-intencionado pode desenvolver um ataque de objetivo de permitir que o usuário mal-intencionado acesso a informações confidenciais ou as credenciais de um usuário.</span><span class="sxs-lookup"><span data-stu-id="f97d4-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="f97d4-117">Para iniciar o ataque, o usuário mal-intencionado convence o usuário clicar em um link para a página de logon do seu site com um `returnUrl` valor de cadeia de consulta adicionada à URL.</span><span class="sxs-lookup"><span data-stu-id="f97d4-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="f97d4-118">Por exemplo, considere um aplicativo na `contoso.com` que inclui uma página de logon no `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="f97d4-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="f97d4-119">O ataque segue estas etapas:</span><span class="sxs-lookup"><span data-stu-id="f97d4-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="f97d4-120">O usuário clica no link para mal-intencionado `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (a segunda URL é "contoso**1**.com", não "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="f97d4-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="f97d4-121">O usuário fizer logon com êxito.</span><span class="sxs-lookup"><span data-stu-id="f97d4-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="f97d4-122">O usuário é redirecionado (pelo site) para `http://contoso1.com/Account/LogOn` (um site mal-intencionado que se parece exatamente com o site real).</span><span class="sxs-lookup"><span data-stu-id="f97d4-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="f97d4-123">O usuário fizer logon novamente (dando mal-intencionado suas credenciais do site) e é redirecionado para o site real.</span><span class="sxs-lookup"><span data-stu-id="f97d4-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="f97d4-124">O usuário provavelmente acredita que sua primeira tentativa de fazer logon falhou e que sua segunda tentativa seja bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="f97d4-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="f97d4-125">O usuário provavelmente permanecerá ciente de que suas credenciais estão comprometidas.</span><span class="sxs-lookup"><span data-stu-id="f97d4-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Processo de ataque de redirecionamento aberto](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="f97d4-127">Além das páginas de logon, alguns sites fornecem páginas de redirecionamento ou pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="f97d4-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="f97d4-128">Imagine que seu aplicativo tem uma página com um redirecionamento aberto, `/Home/Redirect`.</span><span class="sxs-lookup"><span data-stu-id="f97d4-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="f97d4-129">Um invasor pode criar, por exemplo, um link em um email que vai para `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span><span class="sxs-lookup"><span data-stu-id="f97d4-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="f97d4-130">Um usuário típico examinará a URL e ver que ele começa com o nome do site.</span><span class="sxs-lookup"><span data-stu-id="f97d4-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="f97d4-131">Confiar em que, eles clicarem no link.</span><span class="sxs-lookup"><span data-stu-id="f97d4-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="f97d4-132">O redirecionamento aberto, em seguida, enviaria o usuário para o site de phishing, que parece ser idêntico ao seu, e provavelmente o usuário faça logon no que acreditam for seu site.</span><span class="sxs-lookup"><span data-stu-id="f97d4-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="f97d4-133">Proteção contra ataques de redirecionamento abertos</span><span class="sxs-lookup"><span data-stu-id="f97d4-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="f97d4-134">Ao desenvolver aplicativos da web, trate todos os dados fornecidos pelo usuário como não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="f97d4-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="f97d4-135">Se seu aplicativo tem funcionalidade que redireciona o usuário com base no conteúdo da URL, certifique-se de que tais redirecionamentos são feitos somente localmente dentro de seu aplicativo (ou uma URL conhecida, não qualquer URL que pode ser fornecido na cadeia de consulta).</span><span class="sxs-lookup"><span data-stu-id="f97d4-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="f97d4-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="f97d4-136">LocalRedirect</span></span>

<span data-ttu-id="f97d4-137">Use o `LocalRedirect` o método auxiliar da base `Controller` classe:</span><span class="sxs-lookup"><span data-stu-id="f97d4-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="f97d4-138">`LocalRedirect` lançará uma exceção se uma URL de local não for especificada.</span><span class="sxs-lookup"><span data-stu-id="f97d4-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="f97d4-139">Caso contrário, ele se comporta exatamente como o `Redirect` método.</span><span class="sxs-lookup"><span data-stu-id="f97d4-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="f97d4-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="f97d4-140">IsLocalUrl</span></span>

<span data-ttu-id="f97d4-141">Use o [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) método para testar a antes de redirecionar URLs:</span><span class="sxs-lookup"><span data-stu-id="f97d4-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="f97d4-142">O exemplo a seguir mostra como verificar se uma URL é local antes de redirecionar.</span><span class="sxs-lookup"><span data-stu-id="f97d4-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="f97d4-143">O `IsLocalUrl` método protege os usuários contra inadvertidamente sendo redirecionado para um site mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="f97d4-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="f97d4-144">Você pode registrar os detalhes da URL que foi fornecido quando uma URL de local não é fornecida em uma situação em que você esperava uma URL local.</span><span class="sxs-lookup"><span data-stu-id="f97d4-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="f97d4-145">URLs de redirecionamento de registro em log podem ajudar no diagnóstico de ataques de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="f97d4-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
