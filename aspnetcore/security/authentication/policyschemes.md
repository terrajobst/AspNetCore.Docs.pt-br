---
title: Esquemas de política no ASP.NET Core
author: rick-anderson
description: Esquemas de política de autenticação torná-lo mais fácil ter um esquema de autenticação lógica
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: c310b61e14df2b7846e32a602bb75914a5850aff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895193"
---
# <a name="policy-schemes-in-aspnet-core"></a>Esquemas de política no ASP.NET Core

Esquemas de política de autenticação torná-lo mais fácil ter um esquema de autenticação lógica potencialmente usar várias abordagens. Por exemplo, um esquema de política pode usar autenticação do Google para desafios e autenticação de cookie para todo o resto. Esquemas de política de autenticação torná-lo:

* Fácil encaminhar qualquer ação de autenticação para outro esquema.
* Encaminhar dinamicamente com base na solicitação.

Todos os esquemas de autenticação que use derivado <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> e os respectivos [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* São automaticamente esquemas de política no ASP.NET Core 2.1 e versões posteriores.
* Pode ser habilitado por meio de configuração de opções do esquema.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra um esquema de nível superior que combina os esquemas de nível inferiores. Autenticação do Google é usada para os desafios e autenticação de cookie é usada para todo o resto:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

O exemplo a seguir habilita a seleção dinâmica de esquemas em uma base por solicitação. Ou seja, como a combinação de cookies e autenticação de API.

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
