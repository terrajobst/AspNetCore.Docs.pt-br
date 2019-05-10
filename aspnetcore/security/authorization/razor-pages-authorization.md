---
title: Convenções de autorização de páginas do Razor no ASP.NET Core
author: guardrex
description: Saiba como controlar o acesso a páginas com as convenções de autorizam usuários e permitir que usuários anônimos acessem páginas ou pastas de páginas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ff061f96f30cd893b903403de760a172c924cf06
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895413"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Convenções de autorização de páginas do Razor no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Uma maneira de controlar o acesso em seu aplicativo de páginas do Razor é usar as convenções de autorização na inicialização. Essas convenções permitem que você autorizar usuários e permitir que usuários anônimos acessem páginas individuais ou pastas de páginas. Aplicam as convenções descritas neste tópico automaticamente [filtros de autorização](xref:mvc/controllers/filters#authorization-filters) para controlar o acesso.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo usa [autenticação de cookie sem o ASP.NET Core Identity](xref:security/authentication/cookie). Os conceitos e os exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam o ASP.NET Core Identity. Para usar a identidade do ASP.NET Core, siga as orientações em <xref:security/authentication/identity>.

## <a name="require-authorization-to-access-a-page"></a>Exigir autorização para acessar uma página

Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para a página no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor sem uma extensão e que contém apenas barras "/".

Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizePage sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pode ser aplicado a uma classe de modelo de página com o `[Authorize]` atributo de filtro. Para obter mais informações, consulte [atributo de filtro Authorize](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Exigir autorização para acessar uma pasta de páginas

Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para todas as páginas em uma pasta no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor.

Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizeFolder sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Exigir autorização para acessar uma página de área

Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para a página de área no caminho especificado:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

O nome da página é o caminho do arquivo sem uma extensão relativo ao diretório raiz páginas para a área especificada. Por exemplo, o nome da página para o arquivo *Areas/Identity/Pages/Manage/Accounts.cshtml* é *contas/gerenciar/*.

Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizeAreaPage sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Exigir autorização para acessar uma pasta de áreas

Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para todas as áreas em uma pasta no caminho especificado:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

O caminho da pasta é o caminho da pasta, relativo ao diretório raiz páginas para a área especificada. Por exemplo, o caminho da pasta para os arquivos sob *áreas/identidade/páginas/gerenciar/* é */gerenciar*.

Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizeAreaFolder sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Permitir o acesso anônimo para uma página

Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> para uma página no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor sem uma extensão e que contém apenas barras "/".

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Permitir o acesso anônimo para uma pasta de páginas

Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> para todas as páginas em uma pasta no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Observação sobre combinação autorizada e acesso anônimo

Ele é válido para especificar que uma pasta de páginas que exigem autorização e que especifique que uma página dentro da pasta permite acesso anônimo:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

O inverso, no entanto, não é válido. Você não pode declarar uma pasta de páginas para acesso anônimo e, em seguida, especifique uma página dentro dessa pasta que requer autorização:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Exigir autorização na página privada falhar. Quando tanto o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> são aplicadas para a página, o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> terá precedência e controla o acesso.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>
