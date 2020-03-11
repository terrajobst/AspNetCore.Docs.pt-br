---
title: Impedir ataques de redirecionamento abertos no ASP.NET Core
author: ardalis
description: Mostra como evitar ataques de redirecionamento abertos em um aplicativo ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660520"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="467c3-103">Impedir ataques de redirecionamento abertos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="467c3-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="467c3-104">Um aplicativo Web que redireciona para uma URL especificada por meio da solicitação, como a QueryString ou os dados de formulário, pode ser adulterado para redirecionar os usuários para uma URL externa e mal-intencionada.</span><span class="sxs-lookup"><span data-stu-id="467c3-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="467c3-105">Essa violação é chamada de ataque de redirecionamento aberto.</span><span class="sxs-lookup"><span data-stu-id="467c3-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="467c3-106">Sempre que a lógica do aplicativo redireciona para uma URL especificada, você deve verificar se a URL de redirecionamento não foi violada.</span><span class="sxs-lookup"><span data-stu-id="467c3-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="467c3-107">ASP.NET Core tem funcionalidade interna para ajudar a proteger aplicativos contra ataques de redirecionamento aberto (também conhecidos como redirecionamento aberto).</span><span class="sxs-lookup"><span data-stu-id="467c3-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="467c3-108">O que é um ataque de redirecionamento aberto?</span><span class="sxs-lookup"><span data-stu-id="467c3-108">What is an open redirect attack?</span></span>

<span data-ttu-id="467c3-109">Os aplicativos Web geralmente redirecionam os usuários para uma página de logon quando acessam recursos que exigem autenticação.</span><span class="sxs-lookup"><span data-stu-id="467c3-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="467c3-110">O redirecionamento normalmente inclui um parâmetro `returnUrl` QueryString para que o usuário possa ser retornado para a URL solicitada originalmente depois de ter feito logon com êxito.</span><span class="sxs-lookup"><span data-stu-id="467c3-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="467c3-111">Depois que o usuário é autenticado, ele é redirecionado para a URL que ele solicitou originalmente.</span><span class="sxs-lookup"><span data-stu-id="467c3-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="467c3-112">Como a URL de destino é especificada na QueryString da solicitação, um usuário mal-intencionado pode adulterar a QueryString.</span><span class="sxs-lookup"><span data-stu-id="467c3-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="467c3-113">Uma QueryString violada pode permitir que o site redirecione o usuário para um site externo e mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="467c3-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="467c3-114">Essa técnica é chamada de ataque de redirecionamento aberto (ou redirecionamento).</span><span class="sxs-lookup"><span data-stu-id="467c3-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="467c3-115">Um ataque de exemplo</span><span class="sxs-lookup"><span data-stu-id="467c3-115">An example attack</span></span>

<span data-ttu-id="467c3-116">Um usuário mal-intencionado pode desenvolver um ataque destinado a permitir que o usuário mal-intencionado acesse as credenciais de um usuário ou informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="467c3-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="467c3-117">Para iniciar o ataque, o usuário mal-intencionado convencer o usuário a clicar em um link para a página de logon do site com um `returnUrl` valor de QueryString adicionado à URL.</span><span class="sxs-lookup"><span data-stu-id="467c3-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="467c3-118">Por exemplo, considere um aplicativo em `contoso.com` que inclui uma página de logon no `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="467c3-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="467c3-119">O ataque segue estas etapas:</span><span class="sxs-lookup"><span data-stu-id="467c3-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="467c3-120">O usuário clica em um link mal-intencionado para `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (a segunda URL é "Contoso**1**. com", não "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="467c3-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="467c3-121">O usuário faz logon com êxito.</span><span class="sxs-lookup"><span data-stu-id="467c3-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="467c3-122">O usuário é redirecionado (pelo site) para `http://contoso1.com/Account/LogOn` (um site mal-intencionado que é exatamente como um site real).</span><span class="sxs-lookup"><span data-stu-id="467c3-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="467c3-123">O usuário faz logon novamente (concedendo ao site mal-intencionado suas credenciais) e é Redirecionado de volta para o site real.</span><span class="sxs-lookup"><span data-stu-id="467c3-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="467c3-124">O usuário provavelmente acredita que a primeira tentativa de fazer logon falhou e que a segunda tentativa é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="467c3-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="467c3-125">O usuário provavelmente ainda não sabe que suas credenciais estão comprometidas.</span><span class="sxs-lookup"><span data-stu-id="467c3-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Abrir processo de ataque de redirecionamento](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="467c3-127">Além das páginas de logon, alguns sites fornecem páginas de redirecionamento ou pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="467c3-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="467c3-128">Imagine que seu aplicativo tenha uma página com um redirecionamento aberto, `/Home/Redirect`.</span><span class="sxs-lookup"><span data-stu-id="467c3-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="467c3-129">Um invasor pode criar, por exemplo, um link em um email que vai para `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span><span class="sxs-lookup"><span data-stu-id="467c3-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="467c3-130">Um usuário típico irá examinar a URL e vê-la começar com o nome do site.</span><span class="sxs-lookup"><span data-stu-id="467c3-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="467c3-131">Confiando nele, eles clicarão no link.</span><span class="sxs-lookup"><span data-stu-id="467c3-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="467c3-132">O redirecionamento aberto, em seguida, enviaria o usuário para o site de phishing, que parece ser idêntico ao seu, e o usuário provavelmente fazer logon no que eles acreditam ser seu site.</span><span class="sxs-lookup"><span data-stu-id="467c3-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="467c3-133">Protegendo contra ataques de redirecionamento abertos</span><span class="sxs-lookup"><span data-stu-id="467c3-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="467c3-134">Ao desenvolver aplicativos Web, trate todos os dados fornecidos pelo usuário como não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="467c3-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="467c3-135">Se seu aplicativo tiver funcionalidade que redireciona o usuário com base no conteúdo da URL, verifique se esses redirecionamentos só são feitos localmente no seu aplicativo (ou em uma URL conhecida, e não qualquer URL que possa ser fornecida na QueryString).</span><span class="sxs-lookup"><span data-stu-id="467c3-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="467c3-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="467c3-136">LocalRedirect</span></span>

<span data-ttu-id="467c3-137">Use o método auxiliar `LocalRedirect` da classe base `Controller`:</span><span class="sxs-lookup"><span data-stu-id="467c3-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="467c3-138">`LocalRedirect` gerará uma exceção se uma URL não local for especificada.</span><span class="sxs-lookup"><span data-stu-id="467c3-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="467c3-139">Caso contrário, ele se comporta exatamente como o método `Redirect`.</span><span class="sxs-lookup"><span data-stu-id="467c3-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="467c3-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="467c3-140">IsLocalUrl</span></span>

<span data-ttu-id="467c3-141">Use o método [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) para testar URLs antes de redirecionar:</span><span class="sxs-lookup"><span data-stu-id="467c3-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="467c3-142">O exemplo a seguir mostra como verificar se uma URL é local antes do redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="467c3-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="467c3-143">O método `IsLocalUrl` protege os usuários de serem redirecionados inadvertidamente para um site mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="467c3-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="467c3-144">Você pode registrar em log os detalhes da URL que foi fornecida quando uma URL não local é fornecida em uma situação em que você esperava uma URL local.</span><span class="sxs-lookup"><span data-stu-id="467c3-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="467c3-145">As URLs de redirecionamento de log podem ajudar no diagnóstico de ataques de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="467c3-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
