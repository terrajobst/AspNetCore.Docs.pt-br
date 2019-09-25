---
title: Versão de compatibilidade do ASP.NET Core MVC
author: rick-anderson
description: Saiba como a classe Startup no ASP.NET Core configura serviços e o pipeline de solicitação do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: 35e3b6acba2bc9a0b863bd6d1e96365328b5f169
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256157"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>Versão de compatibilidade do ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-3.0"

O <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> método é não operacional para aplicativos ASP.NET Core 3,0. Ou seja, chamar `SetCompatibilityVersion` com qualquer valor de <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> não afeta o aplicativo.

* A próxima versão secundária do ASP.NET Core pode fornecer um novo `CompatibilityVersion` valor.
* `CompatibilityVersion`os valores `Version_2_0`sãomarcados. `Version_2_2` `[Obsolete(...)]`
* Veja [alterações de API de interrupção em antifalsificação, CORS, diagnóstico, MVC e roteamento](https://github.com/aspnet/Announcements/issues/387). Essa lista inclui alterações significativas para as opções de compatibilidade.

Para ver como `SetCompatibilityVersion` o funciona com os aplicativos ASP.NET Core 2. x, selecione a [versão ASP.NET Core 2,2 deste artigo](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> método permite que um aplicativo ASP.NET Core 2. x aceite ou recuse alterações de comportamento potencialmente quebradas introduzidas no ASP.NET Core MVC 2,1 ou 2,2. Essas possíveis alterações da falha de comportamento geralmente afetam como o subsistema MVC se comporta e como **o código do usuário** é chamado pelo tempo de execução. Ao aceitar, você obtém o comportamento mais recente e o comportamento de longo prazo do ASP.NET Core.

O código a seguir define o modo de compatibilidade para o ASP.NET Core 2.2:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

É recomendável que você teste seu aplicativo usando a versão mais recente (`CompatibilityVersion.Latest`). Estimamos que a maioria dos aplicativos não terão alterações da falha de comportamento usando a versão mais recente.

Os aplicativos que `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` chamam são protegidos contra alterações de comportamento potencialmente quebras introduzidas nas versões MVC do ASP.NET Core 2.1/2.2. Essa proteção:

* Não se aplica a todas as alterações da 2.1 e posteriores, ela é direcionada às possíveis alterações da falha de comportamento do tempo de execução do ASP.NET Core no subsistema de MVC.
* Não estende para o ASP.NET Core 3,0.

A compatibilidade padrão para os aplicativos ASP.NET Core 2,1 e 2,2 que **não** chamam `SetCompatibilityVersion` é a compatibilidade 2,0. Ou seja, não chamar `SetCompatibilityVersion` é o mesmo que chamar `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.

O código a seguir define o modo de compatibilidade para o ASP.NET Core 2.2, exceto para os seguintes comportamentos:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

Para aplicativos que encontram alterações da falha de comportamento, o uso das opções de compatibilidade apropriadas:

* Permite que você use a versão mais recente e recuse alterações da falha de comportamento específicas.
* Tenha tempo para atualizar seu aplicativo para que ele funcione com as alterações mais recentes.

A documentação <xref:Microsoft.AspNetCore.Mvc.MvcOptions> tem uma boa explicação sobre o que mudou e por que as alterações são uma melhoria para a maioria dos usuários.

Com o ASP.NET Core 3,0, os comportamentos antigos com suporte das opções de compatibilidade foram removidos. Consideramos que essas são alterações positivas que beneficiam quase todos os usuários. Ao introduzir essas alterações em 2,1 e 2,2, a maioria dos aplicativos pode se beneficiar, enquanto outras têm tempo para atualizar.
::: moniker-end