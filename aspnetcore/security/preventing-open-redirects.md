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
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Impedir ataques de redirecionamento abertos no ASP.NET Core

Um aplicativo Web que redireciona para uma URL especificada por meio da solicitação, como a QueryString ou os dados de formulário, pode ser adulterado para redirecionar os usuários para uma URL externa e mal-intencionada. Essa violação é chamada de ataque de redirecionamento aberto.

Sempre que a lógica do aplicativo redireciona para uma URL especificada, você deve verificar se a URL de redirecionamento não foi violada. ASP.NET Core tem funcionalidade interna para ajudar a proteger aplicativos contra ataques de redirecionamento aberto (também conhecidos como redirecionamento aberto).

## <a name="what-is-an-open-redirect-attack"></a>O que é um ataque de redirecionamento aberto?

Os aplicativos Web geralmente redirecionam os usuários para uma página de logon quando acessam recursos que exigem autenticação. O redirecionamento normalmente inclui um parâmetro `returnUrl` QueryString para que o usuário possa ser retornado para a URL solicitada originalmente depois de ter feito logon com êxito. Depois que o usuário é autenticado, ele é redirecionado para a URL que ele solicitou originalmente.

Como a URL de destino é especificada na QueryString da solicitação, um usuário mal-intencionado pode adulterar a QueryString. Uma QueryString violada pode permitir que o site redirecione o usuário para um site externo e mal-intencionado. Essa técnica é chamada de ataque de redirecionamento aberto (ou redirecionamento).

### <a name="an-example-attack"></a>Um ataque de exemplo

Um usuário mal-intencionado pode desenvolver um ataque destinado a permitir que o usuário mal-intencionado acesse as credenciais de um usuário ou informações confidenciais. Para iniciar o ataque, o usuário mal-intencionado convencer o usuário a clicar em um link para a página de logon do site com um `returnUrl` valor de QueryString adicionado à URL. Por exemplo, considere um aplicativo em `contoso.com` que inclui uma página de logon no `http://contoso.com/Account/LogOn?returnUrl=/Home/About`. O ataque segue estas etapas:

1. O usuário clica em um link mal-intencionado para `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (a segunda URL é "Contoso**1**. com", não "contoso.com").
2. O usuário faz logon com êxito.
3. O usuário é redirecionado (pelo site) para `http://contoso1.com/Account/LogOn` (um site mal-intencionado que é exatamente como um site real).
4. O usuário faz logon novamente (concedendo ao site mal-intencionado suas credenciais) e é Redirecionado de volta para o site real.

O usuário provavelmente acredita que a primeira tentativa de fazer logon falhou e que a segunda tentativa é bem-sucedida. O usuário provavelmente ainda não sabe que suas credenciais estão comprometidas.

![Abrir processo de ataque de redirecionamento](preventing-open-redirects/_static/open-redirection-attack-process.png)

Além das páginas de logon, alguns sites fornecem páginas de redirecionamento ou pontos de extremidade. Imagine que seu aplicativo tenha uma página com um redirecionamento aberto, `/Home/Redirect`. Um invasor pode criar, por exemplo, um link em um email que vai para `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`. Um usuário típico irá examinar a URL e vê-la começar com o nome do site. Confiando nele, eles clicarão no link. O redirecionamento aberto, em seguida, enviaria o usuário para o site de phishing, que parece ser idêntico ao seu, e o usuário provavelmente fazer logon no que eles acreditam ser seu site.

## <a name="protecting-against-open-redirect-attacks"></a>Protegendo contra ataques de redirecionamento abertos

Ao desenvolver aplicativos Web, trate todos os dados fornecidos pelo usuário como não confiáveis. Se seu aplicativo tiver funcionalidade que redireciona o usuário com base no conteúdo da URL, verifique se esses redirecionamentos só são feitos localmente no seu aplicativo (ou em uma URL conhecida, e não qualquer URL que possa ser fornecida na QueryString).

### <a name="localredirect"></a>LocalRedirect

Use o método auxiliar `LocalRedirect` da classe base `Controller`:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` gerará uma exceção se uma URL não local for especificada. Caso contrário, ele se comporta exatamente como o método `Redirect`.

### <a name="islocalurl"></a>IsLocalUrl

Use o método [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) para testar URLs antes de redirecionar:

O exemplo a seguir mostra como verificar se uma URL é local antes do redirecionamento.

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

O método `IsLocalUrl` protege os usuários de serem redirecionados inadvertidamente para um site mal-intencionado. Você pode registrar em log os detalhes da URL que foi fornecida quando uma URL não local é fornecida em uma situação em que você esperava uma URL local. As URLs de redirecionamento de log podem ajudar no diagnóstico de ataques de redirecionamento.
