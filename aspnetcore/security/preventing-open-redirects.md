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
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Impedir ataques de redirecionamento aberto no ASP.NET Core

Um aplicativo web que redireciona para uma URL que é especificada por meio de solicitação, como os dados de formulário ou cadeia de consulta potencialmente pode ser violado para redirecionar usuários para uma URL externa e mal-intencionado. Essa violação é chamado de um ataque de redirecionamento aberto.

Sempre que a lógica do aplicativo redireciona para uma URL especificada, verifique se a URL de redirecionamento não foi adulterada. O ASP.NET Core tem funcionalidade interna para ajudar a proteger aplicativos contra ataques de redirecionamento aberto (também conhecido como open redirecionamento).

## <a name="what-is-an-open-redirect-attack"></a>O que é um ataque de redirecionamento aberto?

Aplicativos Web com frequência redirecionar os usuários para uma página de logon quando acessarem os recursos que exigem a autenticação. O redirecionamento normalmente inclui uma `returnUrl` parâmetro querystring para que o usuário pode ser retornado para a URL solicitada originalmente depois que eles fizeram logon com êxito. Depois que o usuário é autenticado, ele serão redirecionados para a URL que eles solicitaram originalmente.

Como a URL de destino é especificada na sequência de consulta da solicitação, um usuário mal-intencionado pode violar a querystring. Uma cadeia de consulta violada pode permitir que o site redirecionar o usuário para um site externo, mal-intencionado. Essa técnica é chamada de um ataque de redirecionamento (ou redirecionamento) aberto.

### <a name="an-example-attack"></a>Um ataque de exemplo

Um usuário mal-intencionado pode desenvolver um ataque de objetivo de permitir que o usuário mal-intencionado acesso a informações confidenciais ou as credenciais de um usuário. Para iniciar o ataque, o usuário mal-intencionado convence o usuário clicar em um link para a página de logon do seu site com um `returnUrl` valor de cadeia de consulta adicionada à URL. Por exemplo, considere um aplicativo na `contoso.com` que inclui uma página de logon no `http://contoso.com/Account/LogOn?returnUrl=/Home/About`. O ataque segue estas etapas:

1. O usuário clica no link para mal-intencionado `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (a segunda URL é "contoso**1**.com", não "contoso.com").
2. O usuário fizer logon com êxito.
3. O usuário é redirecionado (pelo site) para `http://contoso1.com/Account/LogOn` (um site mal-intencionado que se parece exatamente com o site real).
4. O usuário fizer logon novamente (dando mal-intencionado suas credenciais do site) e é redirecionado para o site real.

O usuário provavelmente acredita que sua primeira tentativa de fazer logon falhou e que sua segunda tentativa seja bem-sucedida. O usuário provavelmente permanecerá ciente de que suas credenciais estão comprometidas.

![Processo de ataque de redirecionamento aberto](preventing-open-redirects/_static/open-redirection-attack-process.png)

Além das páginas de logon, alguns sites fornecem páginas de redirecionamento ou pontos de extremidade. Imagine que seu aplicativo tem uma página com um redirecionamento aberto, `/Home/Redirect`. Um invasor pode criar, por exemplo, um link em um email que vai para `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`. Um usuário típico examinará a URL e ver que ele começa com o nome do site. Confiar em que, eles clicarem no link. O redirecionamento aberto, em seguida, enviaria o usuário para o site de phishing, que parece ser idêntico ao seu, e provavelmente o usuário faça logon no que acreditam for seu site.

## <a name="protecting-against-open-redirect-attacks"></a>Proteção contra ataques de redirecionamento abertos

Ao desenvolver aplicativos da web, trate todos os dados fornecidos pelo usuário como não confiáveis. Se seu aplicativo tem funcionalidade que redireciona o usuário com base no conteúdo da URL, certifique-se de que tais redirecionamentos são feitos somente localmente dentro de seu aplicativo (ou uma URL conhecida, não qualquer URL que pode ser fornecido na cadeia de consulta).

### <a name="localredirect"></a>LocalRedirect

Use o `LocalRedirect` o método auxiliar da base `Controller` classe:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` lançará uma exceção se uma URL de local não for especificada. Caso contrário, ele se comporta exatamente como o `Redirect` método.

### <a name="islocalurl"></a>IsLocalUrl

Use o [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) método para testar a antes de redirecionar URLs:

O exemplo a seguir mostra como verificar se uma URL é local antes de redirecionar.

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

O `IsLocalUrl` método protege os usuários contra inadvertidamente sendo redirecionado para um site mal-intencionado. Você pode registrar os detalhes da URL que foi fornecido quando uma URL de local não é fornecida em uma situação em que você esperava uma URL local. URLs de redirecionamento de registro em log podem ajudar no diagnóstico de ataques de redirecionamento.
